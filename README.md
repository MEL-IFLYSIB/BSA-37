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
- Standard force field files (e.g., CHARMM36m – specify if different)

## Repository Contents

- `topol.top` – System topology
- `gro/` – Coordinate files for each urea concentration
- `mdp/` – Run parameter files for:
  - Energy minimization
  - NVT equilibration
  - NPT equilibration
  - Production MD
- `index.ndx` – Index file for group definitions (if used)

## Usage

1. Clone the repository:
   ```bash
   git clone https://github.com/manuxch/bsa37-md.git
   cd bsa37-md
   ```
2. Select the desired urea concentration folder (e.g., 1M/, 3M/, etc.).
3. Run the simulation pipeline:
  # Minimization
  gmx grompp -f mdp/em.mdp -c gro/start.gro -p topol.top -o em.tpr
  gmx mdrun -deffnm em -v

  # NVT equilibration
  gmx grompp -f mdp/nvt.mdp -c em.gro -r em.gro -p topol.top -o nvt.tpr
  gmx mdrun -deffnm nvt -v

  # NPT equilibration
  gmx grompp -f mdp/npt.mdp -c nvt.gro -r nvt.gro -p topol.top -o npt.tpr
  gmx mdrun -deffnm npt -v

  # Production run
  gmx grompp -f mdp/md.mdp -c npt.gro -p topol.top -o md.tpr
  gmx mdrun -deffnm md -v
  
## Reproducibility

All simulations were performed with GROMACS 2023.4 on NVIDIA GPUs. For exact reproducibility, we recommend using the same software versions.

## Citation

If you use these files in your research, please cite the original article:

    Espinosa, Y. R., Carlevaro, C. M., & Ferrara, C. G. (2026). Urea-Mediated Solvent Reorganization of Bovine Serum Albumin in an Acid-Induced Expanded Conformation at pH 3.7. arXiv preprint arXiv:2605.10444.
    
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
- C. Manuel Carlevaro (@manuxch)
- C. Gastón Ferrara
