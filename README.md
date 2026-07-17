# BSA37 MD Configuration Files

## Supplementary Material for:
**"Urea-Mediated Solvent Reorganization of Bovine Serum Albumin in an Acid-Induced Expanded Conformation at pH 3.7"**  
*Y. Ricardo Espinosa, C. Manuel Carlevaro, and C. Gastón Ferrara*

Preprint available on arXiv: [https://arxiv.org/abs/2605.10444](https://arxiv.org/abs/2605.10444)

---

## Overview

This repository contains the full set of input configuration files required to reproduce the molecular dynamics (MD) simulations of the **BSA37 system** (Bovine Serum Albumin in its F isoform at pH 3.7) reported in the above study.

The simulations explore urea concentrations ranging from 0 M (pure water) to 7 M, analyzing solvent reorganization around the protein.

> **Important note:** The files provided here correspond to a **single replicate** of the simulation. The statistical analysis presented in the paper is based on **10 independent replicates** for each urea concentration. To reproduce the full dataset, you must run these same inputs with different initial velocities (or random seeds) to generate the remaining nine replicates.

## Requirements

- **GROMACS** version 2023.4 (or later, with compatible syntax)
- **NVIDIA GPU** with CUDA support (for accelerated runs)
- GROMOS 54A7 force field

## Repository Contents

- `*.top` – System topology
- `*.gro` – Coordinate files for each urea concentration
- `*.mdp` – Run parameter files for:
  - Energy minimization
  - NVT equilibration
  - NPT equilibration
  - Production MD

## Usage

### 1. Clone the repository

```bash
git clone https://github.com/manuxch/bsa37-md.git
cd bsa37-md
```

The repository provides preprocessed GROMACS run-input files (`.tpr`) for the following systems:

| Directory | System |
|---|---|
| `CONTROLW/` | BSA in pure water (0 M urea) |
| `UREA1M/` | BSA in 1 M urea |
| `UREA2M/` | BSA in 2 M urea |
| `UREA3M/` | BSA in 3 M urea |
| `UREA4M/` | BSA in 4 M urea |
| `UREA5M/` | BSA in 5 M urea |
| `UREA7M/` | BSA in 7 M urea |
| `CONTROLU/` | Control system in pure urea |

### 2. Run an included preprocessed system

The simplest way to run a system is to use the corresponding `.tpr` file directly. For example, to run the 1 M urea system:

```bash
cd UREA1M
gmx mdrun -s bsa-ph37-urea-1M-1.tpr -deffnm bsa-ph37-urea-1M-1 -v
```

Equivalent commands can be used for the other systems by replacing the directory and file prefix. For example:

```bash
# Pure-water control
cd CONTROLW
gmx mdrun -s bsa-ph37-urea-0M-1.tpr -deffnm bsa-ph37-urea-0M-1 -v

# 5 M urea
cd UREA5M
gmx mdrun -s bsa-ph37-urea-5M-1.tpr -deffnm bsa-ph37-urea-5M-1 -v
```

The `.mdp` files in `CONTROLW/`, `CONTROLU/`, and `UREA1M/`--`UREA7M/` contain `5,000,000` steps with a timestep of `0.002 ps`, corresponding to **10 ns per run**.

For GPU-accelerated execution, a typical command is:

```bash
gmx mdrun -s bsa-ph37-urea-1M-1.tpr \
  -deffnm bsa-ph37-urea-1M-1 \
  -nb gpu -pme gpu -bonded gpu -update gpu -v
```

The optimal GPU options depend on the installed GROMACS version and hardware configuration.

### 3. Regenerate a 500 ns production `.tpr`

The directory `ESTABILIZACION-PRODUCCION/` contains the coordinate, topology, molecular topology, urea parameter, and production-parameter files required to regenerate the production input. Its `bsa-ph7-urea.mdp` file uses `250,000,000` steps at `0.002 ps`, corresponding to **500 ns**.

Move to that directory and select the desired concentration. For example, for the 1 M system:

```bash
cd ESTABILIZACION-PRODUCCION

gmx grompp \
  -f bsa-ph7-urea.mdp \
  -c bsa-ph37-urea-1Mini.gro \
  -p bsa-ph37-urea-1M.top \
  -o bsa-ph37-urea-1M-production.tpr

gmx mdrun \
  -s bsa-ph37-urea-1M-production.tpr \
  -deffnm bsa-ph37-urea-1M-production \
  -v
```

Use the corresponding filename prefix for the other systems:

```text
0 M:       bsa-ph37-urea-0M
1 M:       bsa-ph37-urea-1M
2 M:       bsa-ph37-urea-2M
3 M:       bsa-ph37-urea-3M
4 M:       bsa-ph37-urea-4M
5 M:       bsa-ph37-urea-5M
7 M:       bsa-ph37-urea-7M
pure urea: bsa-ph37-urea-puro
```

For the pure-urea control, note that the coordinate filename contains an additional hyphen:

```bash
gmx grompp \
  -f bsa-ph7-urea.mdp \
  -c bsa-ph37-urea-puro-ini.gro \
  -p bsa-ph37-urea-puro.top \
  -o bsa-ph37-urea-puro-production.tpr
```

### 4. Generate independent replicates

The supplied inputs represent one replicate. Independent replicates should be generated using different initial velocities. Before creating each new `.tpr`, change `gen-seed` in a copy of `bsa-ph7-urea.mdp`, or set:

```ini
gen-vel  = yes
gen-seed = -1
```

A value of `-1` instructs GROMACS to generate a new random seed when `grompp` is executed. Store every replicate in a separate directory to prevent output files from being overwritten.

### 5. Continue an interrupted run

If a checkpoint file was produced, continue the simulation with:

```bash
gmx mdrun -deffnm bsa-ph37-urea-1M-production -cpi bsa-ph37-urea-1M-production.cpt -append -v
```

Use the actual output prefix of the system being continued.
  
## Reproducibility

All simulations were performed with GROMACS 2023.4 on NVIDIA GPUs. For exact reproducibility, we recommend using the same software versions.

## Citation

If you use these files in your research, please cite the original article:

    Espinosa, Y. R., Carlevaro, C. M., & Ferrara, C. G. (2026). Urea-Mediated Solvent 
    Reorganization of Bovine Serum Albumin in an Acid-Induced Expanded Conformation 
    at pH 3.7. arXiv preprint arXiv:2605.10444.

Bibtex:
```bibtex
  @misc{espinosa2026,
        title={Urea-Mediated Solvent Reorganization of Bovine Serum Albumin in an Acid-Induced Expanded Conformation at pH 3.7}, 
        author={Y. Ricardo Espinosa and C. Manuel Carlevaro and C. Gaston Ferrara},
        year={2026},
        eprint={2605.10444},
        archivePrefix={arXiv},
        primaryClass={physics.bio-ph},
        url={https://arxiv.org/abs/2605.10444}, 
  }
```

## Contact

For questions or issues, please open an issue on this repository or contact the authors.

## Authors

- Y. Ricardo Espinosa
- C. Manuel Carlevaro ([@manuxch](https://github.com/manuxch))
- C. Gastón Ferrara
