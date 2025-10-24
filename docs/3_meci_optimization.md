# ⏳ MECI Optimization

> 📖 In the **MECI Optimization** step, each cluster’s centroid from the previous stage is refined to locate a **Minimum Energy Conical Intersection (MECI)** using multireference electronic structure methods such as **CASSCF**, **FOMO-CASCI**, or **hhTDA**.

---

## 🧠 Concept Overview

- Each cluster’s **centroid** geometry serves as an initial guess for MECI optimization.
- MECI searches refine the geometry to the point of degeneracy between two electronic states with identical multiplicity.
- The resulting **optimized MECIs** represent key regions of the intersection seam and are used as inputs for **cone sampling**.
- These structures help map the accessible parts of the conical intersection space.

---

## 🧰 Step-By-Step MECI Optimization

1. **Create directories for each centroid**
   ```bash
   mkdir ciopt0 ciopt1 ciopt2 ...
   ```

2. **Move each centroid into its directory**
   ```bash
   mv centroid0.xyz ciopt0/
   mv centroid1.xyz ciopt1/
   ```

3. **Create the TeraChem input file `tc.in`**
   ```bash
   basis 6-31gs
   charge 0
   spinmult 1
   method rhf
   precision mixed
   scf diis+a
   closed 20
   active 5
   casscf yes
   spinmult 1
   cassinglets 4
   castarget 0
   castargetmult 1
   precision mixed
   coordinates centroid0.xyz
   run ciopt
   nacstate1 2
   nacstate2 3
   casguess c0.casscf
   casscftrustmaxiter 1000
   casscfmaxiter 1000
   casscforbnriter 1000
   cpsacasscfmaxiter 1000
   cphfiter 1000
   end
   ```

4. **Add orbital guess files if applicable**
   ```bash
   <molecule>/seam_sampling/clustering/ciopt0/c0
   <molecule>/seam_sampling/clustering/ciopt0/c0.casscf
   ```

5. **Create `submit.sh` for each MECI optimization**
   ```bash
   #!/bin/bash
   #SBATCH -p l40-gpu
   #SBATCH -N 1
   #SBATCH -n 4
   #SBATCH -J centroid0
   #SBATCH --mem=50G
   #SBATCH -t 30:00:00
   #SBATCH --qos gpu_access
   #SBATCH --gres=gpu:1

   module load tc/25.03
   terachem tc.in > tc.out
   ```

6. **Submit jobs for all centroids**
   ```bash
   sbatch submit.sh
   ```

---

## 📦 Outputs

- `scr.centroid#/optim.xyz` → optimized MECI geometry  
- `tc.out` → full TeraChem output log  
- These MECIs are used as input structures for **Cone Sampling**.

---

Continue to [🍦 Cone Sampling →](4_cone_sampling.md)

