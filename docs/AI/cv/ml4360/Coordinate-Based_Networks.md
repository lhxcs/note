# Coordinate-Based Networks

## Implicit Neural Representations

*Can we learn 3D reconstruction from **data***?

### Learning-based 3D Reconstruction

**Voxels**: Discretization of 3D space into grid

- Cubic memory, which leads to limited resolution
- Introduce Manhattan bias（Disadvantage)

**Points**: Discretization of surface into 3D points

- Does not model connectivity/topology
- Limited number of points
- Global shape description

**Meshes**: Discretization into vertices and faces

- Limited number of vertices
- Require class-specific template -
- Leads to self-intersections

**This work**:*Key idea*

- Do not represent 3D shape explicitly
- Instead, consider surface implicitly as desicion boundary of a non-linear classifier: $f_\theta:\mathbb{R}^3\times $