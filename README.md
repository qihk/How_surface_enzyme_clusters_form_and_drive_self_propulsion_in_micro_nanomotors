# How surface enzyme clusters form and drive self-propulsion in micro- and nanomotors

This repository contains custom Python scripts used to construct coarse-grained urease models, initial simulation systems, and colloidal particles with different catalytic surface organizations for the simulations described in our study.

The source code and model-generation scripts are available at:

https://github.com/qihk/How_surface_enzyme_clusters_form_and_drive_self_propulsion_in_micro_nanomotors

## Requirements

The scripts and simulations were tested with:

- CentOS Linux 7 (Core)
- Python 3.13.2
- LAMMPS (22 Jul 2025)
- NumPy 2.3.0
- SciPy 1.15.0
- Biopython 1.85
- scikit-learn 1.8.0

No specialized hardware is required for running the model-generation scripts.

## Installation

No separate installation is required for the provided Python scripts beyond Python and the packages listed above.

The required Python packages can be installed using:

```bash
pip3 install numpy scipy biopython scikit-learn
```

### LAMMPS installation

LAMMPS must be installed separately. The simulations described in the manuscript were performed using **LAMMPS (22 Jul 2025)**. The `RIGID`, `MOLECULE`, and `MC` packages are required for the simulations and must be included when compiling LAMMPS.

A minimal source installation of LAMMPS can be performed as follows.

First, install a C/C++ compiler, `make`, an MPI implementation (e.g. MPICH), and an FFT library (e.g. FFTW) using the system package manager or from source.

Download and extract the LAMMPS source code, then enter the `src` directory:

```bash
tar -zxvf lammps-22Jul2025.tar.gz
cd lammps-22Jul2025/src
```

Enable the required packages:

```bash
make yes-rigid yes-molecule yes-mc
```

Then compile the MPI-enabled executable:

```bash
make mpi -j 16
```

After compilation, the generated `lmp_mpi` executable can be added to the system `PATH`, for example:

```bash
export PATH=/path/to/lammps-22Jul2025/src:$PATH
```

The installation can be checked using:

```bash
lmp_mpi -h
```

The exact MPI and FFTW installation paths and compiler settings depend on the local computing environment.

Typical installation time for the Python dependencies on a standard desktop computer is approximately 10–20 min. Compilation of LAMMPS typically requires approximately 0.5–12 h, depending on the desktop hardware configuration and compilation method.

## Repository contents

### Urease model construction

#### `Urease-CG-generate.py`

Generates the coarse-grained representation of urease from its all-atom structure.

The urease structure can be obtained from the Protein Data Bank:

- PDB ID: **6ZJA**
- Input file: `6ZJA.pdb`
- PDB DOI: https://doi.org/10.2210/pdb6ZJA/pdb

#### `Urease-AA-generate.py`

Generates the initial simulation system containing multiple coarse-grained urease molecules generated using `Urease-CG-generate.py`.

#### `colloid-structure.data`

Contains the structural information of the spherical colloidal particle used in the GPCG simulations and as the base structure for generating different catalytic surface organizations.

### Colloidal motor construction

#### `generate-random.py`

Reads `colloid-structure.data` and generates a colloidal motor with randomly distributed catalytic surface sites.

#### `generate-nbinomial.py`

Reads `colloid-structure.data` and generates a colloidal motor with spatially heterogeneous catalytic sites following a negative-binomial-based clustering model.

#### `generate-Janus.py`

Reads `colloid-structure.data` and generates a Janus motor in which catalytic sites are spatially confined to one side of the colloidal particle.

## GPCG simulation workflow

The coarse-grained GPCG simulations were performed using LAMMPS (22 Jul 2025). The overall workflow was as follows:

1. The coarse-grained urease model was constructed from the all-atom urease structure (PDB ID: 6ZJA) using `Urease-CG-generate.py`.

2. The initial simulation system containing the colloidal particle and multiple coarse-grained urease molecules was generated using `Urease-AA-generate.py`.

3. The generated configuration was imported into LAMMPS. The colloidal particle and individual urease molecules were treated as rigid bodies using the LAMMPS `fix rigid` command.

4. Langevin dynamics was used to control the motion of the coarse-grained urease molecules and colloidal particle.

5. Reactive immobilization of urease on the colloidal surface was modeled through distance-dependent stochastic bond formation between available surface sites and urease reaction centers using the LAMMPS `fix bond/create` command.

6. Following immobilization, the bonded reaction center was converted to a distinct particle type, allowing immobilized enzymes to interact differently from unbound enzymes.

7. The simulations were propagated for the prescribed number of time steps, and the resulting configurations were used to characterize the spatial organization of immobilized enzymes on the colloidal surface.

Detailed descriptions of the interaction parameters, bond-formation criteria, thermostat settings, and other GPCG simulation parameters are provided in the Supplementary Information.

## Demo

### Coarse-grained urease model

To generate the coarse-grained urease model, download the urease structure from the Protein Data Bank (PDB ID: **6ZJA**) and save it as `6ZJA.pdb` in the same directory as `Urease-CG-generate.py`.

Run:

```bash
python3 Urease-CG-generate.py
```

The generated coarse-grained urease structure can subsequently be used to construct the initial GPCG simulation system using `Urease-AA-generate.py`.

### Colloidal motor generation

`colloid-structure.data` serves as the example input for the GPCG model and colloidal motor generation scripts.

To generate a colloidal motor with randomly distributed catalytic surface sites, place `colloid-structure.data` and `generate-random.py` in the same directory and run:

```bash
python3 generate-random.py
```

Clustered and Janus surface configurations can be generated using:

```bash
python3 generate-nbinomial.py
python3 generate-Janus.py
```

The scripts generate structural/data files containing the colloidal particle with the corresponding catalytic and passive surface-site distributions. These configurations can be used as initial structures for subsequent LAMMPS simulations. Detailed descriptions of the MPC implementation and reaction scheme used in the subsequent motor simulations are provided in the Supplementary Information.

### Expected runtime

The model-generation demos typically complete within approximately 1–10 min on a standard desktop computer, depending on the selected model parameters and hardware configuration.

## Usage

Different catalytic surface organizations can be generated using `generate-random.py`, `generate-nbinomial.py`, and `generate-Janus.py`.

Catalytic coverage and surface-distribution parameters can be modified directly in the corresponding Python scripts to generate different surface configurations.

For urease model construction, `Urease-CG-generate.py` converts the all-atom urease structure into the coarse-grained representation, which can subsequently be used by `Urease-AA-generate.py` to construct larger initial simulation systems.

Detailed descriptions of the GPCG model, MPC scheme, reaction model, interaction parameters, and simulation parameters are provided in the Methods and Supplementary Information of the associated manuscript.

### Minimal example input and expected output

Several example files are provided to demonstrate the model-generation workflow and the expected outputs of the corresponding scripts.

For the urease coarse-graining and initial-system construction:

- `urease-cg.xyz` is an example coarse-grained urease structure generated using `Urease-CG-generate.py`.
- `urease-colloid.lammpsdata` is an example initial GPCG simulation system generated using `Urease-AA-generate.py`.

For this example, the lateral simulation-box dimensions were set to $L_x=L_y=30$, and the system contains `Nurease = 1000` coarse-grained urease molecules together with the colloidal particle.

Three additional example files are provided for the different catalytic surface organizations. In all three cases, the catalytic surface fraction was set to 20%, corresponding to `Number_Type2 = int(Nsurface * 0.2)`.

The corresponding example outputs are:

- `random-phi20%.lammpsdata`: a colloidal particle with randomly distributed catalytic surface sites.
- `nbinomial-phi20%.lammpsdata`: a colloidal particle with clustered catalytic surface sites generated using the negative-binomial-based distribution.
- `janus-phi20%.lammpsdata`: a Janus colloidal particle with catalytic surface sites confined to one side of the particle.

These example files provide representative outputs of the catalytic surface-organization procedures and can be used to verify that the corresponding generation scripts run successfully and produce the expected model configurations.

## Reproducibility

The scripts provided in this repository generate the coarse-grained urease models and colloidal surface configurations used in the simulations described in the associated manuscript.

Detailed simulation methods and model parameters are provided in the Methods and Supplementary Information. Additional analysis scripts are available from the corresponding authors upon reasonable request.

## License

The source code in this repository is distributed under the MIT License. See the `LICENSE` file for details.