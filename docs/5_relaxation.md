# 🏖️ Relaxation

> 📖 The **Relaxation** step propagates or optimizes cone point geometries on the lower electronic state to identify possible **ground-state photoproducts**.

---

## Concept Overview

- Each **cone point** generated during cone sampling is used as a starting geometry.
- NANR provides three relaxation workflows:
  1. **Optimization only** (geometry minimization)
  2. **No initial velocity MD + optimization**
  3. **Random initial velocity MD + optimization**
- These methods emulate how molecules relax after nonadiabatic transitions, revealing accessible minima on the ground-state PES.

---

## 🛌 Step-By-Step: Optimization Only

1. From each `cone_sampling/` directory, create an `opt.in` template:
   ```bash
   coordinates start.xyz
   basis 6-31gs
   spinmult 1
   charge 0
   method wb97x
   run minimize
   dftd no
   purify no
   precision mixed
   scf diis+a
   ```

2. Modify or reuse the submission script:
   ```bash
   #!/bin/bash
   #SBATCH -p l40-gpu
   #SBATCH -N 1
   #SBATCH -n 1
   #SBATCH -J opt
   #SBATCH --mem=50G
   #SBATCH -t 10:00:00
   #SBATCH --qos gpu_access
   #SBATCH --gres=gpu:1
   #SBATCH -o slurm-%j.out
   #SBATCH -e slurm-%j.err

   module load tc/25.03
   terachem opt.in > opt.out
   ```

3. Prepare and run your jobs:
   ```bash
   chmod +x submit.sh
   sbatch submit.sh
   ```

4. The final geometry (bottom N+2 lines of `optim.xyz`) corresponds to your optimized **photoproduct**.

---

## 🚶‍♀️ + 🛌 Step-By-Step: No Initial Velocity MD + Optimization

1. Create an `md.in` file:
   ```bash
   coordinates XXX
   basis 6-31gs
   charge 0
   method wb97x
   spinmult 1
   run md
   precision mixed
   nstep 100
   ```

2. Launch MD and follow with optimization:
   ```bash
   sbatch submit.sh
   ```

3. After the MD completes, extract the final coordinates from `scr.coords/coors.xyz`, then run optimization as above.

---

## 🏃‍♀️ + 🛌 Step-By-Step: Random Initial Velocity MD + Optimization

1. Generate random velocity files:
   ```yaml
   filenames:
     work_path: <molecule>/seam_sampling/clustering/ciopt#/cone_sampling/
   velocity_sampling:
     samples_per_point: 5
     added_energy: 1.8289  # in eV
   ```
   ```bash
   python velocity_sampling.py
   ```

2. For each velocity index (`V0`, `V1`, ...), perform MD as in the previous workflow.

3. After MD completion, extract the last coordinates to initialize optimization directories (`O0/`, `O1/`, etc.).

4. Submit optimization jobs for each set:
   ```bash
   sbatch submit.sh
   ```

---

## Outputs

- `opt.out` → TeraChem optimization log  
- `optim.xyz` → converged geometry  
- `scr.start/optim.xyz` → final relaxed structure  

Each relaxed structure corresponds to a **photoproduct** or **intermediate** accessible from a particular cone point direction.

---

Continue back to [🏠 Home →](index.md)

