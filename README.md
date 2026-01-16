# Vectors scripts

This repository contains all input files for MD simulations, together with scripts for extracting and processing **binding site vectors**.

---

## MD simulation files

The directories `human_cyps` and `plant_cyps` include all files required to reproduce the MD simulation trajectories.

Each CYP system contains **seven numbered subdirectories**, ordered from topology generation to production MD.  
The numeric prefix indicates the **execution order**.

Additional supporting files (ion topologies, `.mtb`, `.ifp`, and library files) are located in the `files/` directory.

### Topologies

For each system, three GROMOS topologies are provided:

- **`cyp.top`**  
  Topology where the heme group is *not* bound to the protein.

- **`cyp_heme.top`**  
  Topology where the heme group is bound to the cysteine residue (CYS).

- **`CYP90D1.top`** (example)  
  Topology for the protein with bound heme and Na⁺/Cl⁻ ions.

Equilibration simulations were run using **GROMOS**, and production MD simulations were performed with **GROMACS**.

---

## Binding site vector scripts

All scripts are located in the `scripts/` directory unless stated otherwise.

### Vector generation and analysis

- **`triangular_lattice_sphere.py`**  
  Generates a triangular lattice sphere used for binding site vector generation.  
  **Inputs:** sphere radius, number of triangle subdivisions, output file name  
  **Options:** `--hemisphere` (outputs only the upper hemisphere; used for CYP systems)

- **`surface.py`**  
  Computes binding site vector lengths.  
  **Inputs:**  
  - PQR file  
  - lattice sphere file  
  - output text file name  
  - distance cutoff from the binding site center  
  **Output:** text file containing vector length data

- **`charge.py`**  
  Computes binding site vector charges.  
  **Inputs:** same as `surface.py`, plus a reference charge file  
  **Output:** text file containing vector charge data  

  The reference file (`reference_charges.txt`) contains partial charges for the **GROMOS 54a8 force field**, parsed internally by the script.

- **`normalization.py`**  
  Normalizes vector length or charge outputs to allow direct comparison between binding sites.

- **`combine.py`**  
  Combines normalized vector length and charge data into a single file, providing a complete binding site description.

---

### Trajectory alignment and preprocessing

- **`trans_rot_4i3q.py`**  
  Translates and rotates the reference **4I3Q** PDB structure so that:
  - the heme lies in the *x–y plane*
  - the iron atom is positioned at *(0, 0, 0)*

- **`trans_first_frame.py`**  
  After alignment to the 4I3Q reference, translates the first trajectory frame so that the heme iron is at *(0, 0, 0)*.

- **`align_selected_residues.py`**  
  Aligns trajectory snapshots to the first (aligned) snapshot using selected residues.  
  Executed via:  
  **`run_align_selected_residues.sh`**

---

### Clustering scripts

- **`first_clustering_vectors.py`**  
  Performs first-round clustering of trajectory snapshots based on binding site vectors.  
  **Input:** output from `combine.py` (or any file containing vector length and/or charge data)  
  **Output:** number of clusters, cluster centers, cluster populations

- **`post_first_clustering.py`**  
  Combines exemplars from the first clustering round into a single weighted file.

- **`second_clustering_vectors.py`**  
  Performs second-round clustering on the first-round exemplars.  
  **Input:** output from `post_first_clustering.py`  
  **Output:** number of clusters, cluster centers, cluster populations

- **`MD_clustering.py`**  
  Clusters trajectory snapshots of a single CYP system based on backbone structural overlap.  
  **Inputs:**  
  - MD trajectory (`.xtc`)  
  - corresponding topology (`.pdb`)  
  **Output:** number of clusters, cluster centers, cluster populations

- **`backbone_cealign_rmsd.py`**  
  Generates pairwise RMSD matrices based on backbone similarity across all studied CYPs.  
  Executed via:  
  **`run_backbone_cealign_rmsd.sh`**














