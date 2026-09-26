## Files

### GPCG Simulation Section

- **`Urease-CG-generate.py`**
  Generates the coarse-grained (CG) representation of urease. The atomic structure of urease is taken from the Protein Data Bank (**PDB ID: 6ZJA**). This script converts the all-atom structure into the coarse-grained representation used in the simulations.

- **`Urease-AA-generate.py`**
  Generates the initial simulation configuration of the urease solution environment. Utilizing the coarse-grained structure produced by the previous script, this program places a large number of urease molecules into the simulation box to construct the initial configuration for subsequent molecular dynamics simulations.

### MD-MPC Simulation Section

The following three scripts generate models with three distinct surface distributions. These scripts construct colloidal spheres with different patterns and include explicit solvent particles.

- **`colloid-structure.data`**
  Contains the atomic information of the spherical colloidal particle used as the base structure. This file provides the particle coordinates and structural information required by the colloid-generation scripts below.

- **`generate-random.py`**
  Generates a spherical colloidal particle with **randomly distributed** catalytic or reactive surface sites. This configuration is used to represent a surface without spatial correlations in the distribution of active sites.

- **`generate-nbinomial.py`**
  Generates a spherical colloidal particle with a heterogeneous surface distribution constructed according to a **negative binomial distribution**. This configuration is used to reproduce a patchy surface organization with spatially heterogeneous catalytic-site distributions.

- **`generate-Janus.py`**
  Generates a **Janus-type** colloidal particle. Catalytic or reactive sites are restricted to one hemisphere of the particle, while the opposite hemisphere remains passive.

---

Analysis scripts are available upon reasonable request.
