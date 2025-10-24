# 🔍 Seam Sampling

> 📖 In the **Seam Sampling** step, NANR explores the intersection seam — the region of energetic degeneracy between two electronic states (e.g., S₀/S₁ or S₁/S₂) — using **metadynamics** with a **seam constraint** that penalizes large energy gaps.

---

## Concept Overview

- NANR uses **RMSD-based collective variables (CVs)** to drive the system along the seam.  
- Periodic **Gaussian bias potentials** are added to discourage revisiting previous configurations.  
- The **seam constraint** switches between linear and harmonic forms depending on the instantaneous energy gap, keeping the system near degeneracy.  
- The resulting trajectory of geometries provides a map of the **accessible conical intersection seam**, which will later feed into clustering and MECI optimization.

---

## Step-By-Step Seam Sampling

1. **Create a working directory**
   ```bash
   mkdir <molecule>/seam_sampling
   ```

2. **Add starting structure and optional orbitals**
   - Place your starting geometry (e.g., FC point or MECI):
     ```
     <molecule>/seam_sampling/molecule.xyz
     ```
   - Optionally include orbital guesses:
     ```
     <molecule>/seam_sampling/c0
     <molecule>/seam_sampling/c0.casscf
     ```

3. **Create `seam_sampling.yaml`**
   ```yaml
   filenames:
     geom_xyz: molecule.xyz             # starting xyz file
     output_dir: /<molecule>/seam_sampling
     trajectory: seam_traj.xyz          # output trajectory

   engine:
     type: terachem
     gpus: 0
     cwd: .
     hhtda: False
     options:
       method: rhf
       basis: 6-31gs
       precision: mixed
       scf: diis+a
       casci: 'yes'
       fon: 'yes'
       fon_temperature: 0.15
       closed: 21
       active: 4
       cassinglets: 4
       castarget: 2
       castargetmult: 1
       charge: 0
       cphfiter: 1000
       maxit: 1000

   dynamics:
     isc: "no"
     nsteps: 100000
     T0: 300
     gamma: 0.001
     timestep: 1.0
     constraint_theta: 0.01
     constraint_coeff: 10
     sample_fraction: 1
     sample_threshold: 0.01
     lower_state: 2
     upper_state: 3
     meta: True
     meta_tau: 25
     meta_alpha: 0.002
     meta_gamma: 1.5
     meta_maxE: 1.0
   ```

4. **Create a submission script `submit.sh`**
   ```bash
   #!/bin/bash
   #SBATCH -p l40-gpu
   #SBATCH -N 1
   #SBATCH -n 4
   #SBATCH -J seam
   #SBATCH --mem=75G
   #SBATCH -t 5-00:00:00
   #SBATCH --qos gpu_access
   #SBATCH --gres=gpu:1

   module load tc/25.03
   python3 /<path>/NonadiabaticNanoreactor/nanr/seam_traj.py seam_sampling.yaml
   ```

5. **Submit your seam-sampling job**
   ```bash
   cd <molecule>/seam_sampling
   sbatch submit.sh
   ```

---

## Outputs

- `seam_traj.xyz` → full metadynamics trajectory  
- `full_geom_seam.xyz` → all collected geometries for clustering  
- `log/` (optional) → runtime logs and energy tracking  

---

Continue to [🌌 Clustering →](2_clustering.md)

