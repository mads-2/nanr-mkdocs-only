# 🌌 Clustering

> 📖 In the **Clustering** step, NANR organizes the large set of geometries collected during seam sampling into groups (**clusters**) that represent distinct regions of the intersection seam. Each cluster's **centroid** becomes a starting point for a **MECI optimization**.

---

## Concept Overview

- Each geometry is aligned and compared using rotational and atomic label permutations to account for molecular symmetry.
- The algorithm applies a **clustering method** (typically *k-means*, though **DBSCAN** and others are supported) based on **Euclidean distance** between geometries.
- The **elbow method** determines the optimal number of clusters.
- The **centroid** of each cluster serves as a starting geometry for a **MECI optimization**.
- This step reduces redundancy and captures distinct structural regions along the sampled seam.

---

## Step-By-Step Clustering

1. **Create a clustering directory**
   ```bash
   mkdir <molecule>/seam_sampling/clustering
   ```

2. **Copy your seam trajectory**
   ```bash
   cp ../full_geom_seam.xyz .
   ```

3. **Create `clustering.yaml`**
   ```yaml
   filenames:
     xyz_file: full_geom_seam.xyz     # starting xyz file
     directory_name: /<molecule>/seam_sampling/clustering/
     num_atoms: 16                    # total number of atoms

   cluster_settings:
     cluster_method: kmeans           # can be: kmeans, dbscan, meanshift, affinity_propagation
     k: None                          # None = use elbow method
     ms_bandwidth: None               # bandwidth parameter for Mean Shift
     ap_damping: 0.5                  # damping for Affinity Propagation
     ap_pref_multipler: 1.0           # exemplar multiplier for Affinity Propagation
     dbscan_min: 5                    # minimum samples for DBSCAN
     dbscan_eps: 0.5                  # distance threshold for DBSCAN
     mass_weighted: False
     ignore_H: True                   # ignore hydrogens to speed up clustering
     diverse_sampling: False
     b: 1                             # number of diverse pairs (kmeans/meanshift only)
     outfile: clustered.xyz           # output file (optional)

   plot_settings:
     type: PCA                        # default PCA projection for visualization
     components: 2
   ```

4. **Create `submit.sh`**
   ```bash
   #!/bin/bash
   #SBATCH -p l40-gpu
   #SBATCH -N 1
   #SBATCH -n 4
   #SBATCH -J cluster
   #SBATCH --mem=50G
   #SBATCH -t 10:00:00
   #SBATCH --qos gpu_access
   #SBATCH --gres=gpu:1

   module load tc/25.03
   python3 /<path>/NonadiabaticNanoreactor/clustering/clusterer.py clustering.yaml
   ```

5. **Submit your clustering job**
   ```bash
   cd <molecule>/seam_sampling/clustering
   sbatch submit.sh
   ```

---

## Outputs

- `clustered.xyz` → optional file containing combined cluster outputs  
- `centroid#.xyz` → representative centroid structures  
- `log/` → job output logs and runtime information  

---

Continue to [⏳ MECI Optimization →](3_meci_optimization.md)

