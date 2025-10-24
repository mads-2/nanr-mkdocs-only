# Nonadiabatic Nanoreactor Use Guide
**Last updated:** October 2025  
**Primary Branch:** `major` (used by Pierilab in the Martinezlab repo)

> *This page is an evolving “Best Use Guide” for beginners — please update it whenever YAML formatting or workflow structures change.*

---

## Important External Info

- GitHub: [Nonadiabatic Nanoreactor – major branch](https://github.com/mtzgroup/NonadiabaticNanoreactor/tree/major)
- Paper: [Chemical Science (2021)](https://pubs.rsc.org/en/content/articlelanding/2021/sc/d1sc00775k)

---

## The Science behind NANR
The NANR explores photochemical reactivity by combining *metadynamics* and *seam constraints* to locate conical intersections efficiently.

---

## The Environment
> ❗ Make sure to `conda activate nanr` before running.

---

## Recommended Directory Structure

```bash
<molecule>/
├── seam_sampling/
│   ├── molecule.xyz
│   ├── seam_sampling.yaml
│   ├── seam_traj.xyz
│   └── clustering/
│       ├── full_geom_seam.xyz
│       ├── clustering.yaml
│       ├── ciopt0/
│       │   ├── centroid0.xyz
│       │   ├── tc.in
│       │   ├── submit.sh
│       │   └── cone_sampling/
│       │       ├── ci0.xyz
│       │       ├── sampling.yaml
│       │       ├── ci0_radial_XXXX.xyz
│       │       └── O0/
│       │           ├── opt.in
│       │           ├── submit.sh
│       │           └── scr.start/optim.xyz
└── logs/, figures/, or results/ (optional)
