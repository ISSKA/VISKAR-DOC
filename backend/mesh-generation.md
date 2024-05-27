# Mesh Generation
Visual KARSYS generates 3D meshes of geological units. The meshes are determined by the stratigraphy and the geological data placed on cross-sections. Computation is done using the **[GeoCruncher](https://github.com/ISSKA/geocruncher/)** library.

## Input
* Cross-sections
* Stratigraphy (Column)
* Contact points
* Dip (orientation)

Requirements for computation:
* One digital elevation model (DEM)
* One dip per series
* One contact data per unit
* One contact data and dip per active fault

## Output
One 3D mesh for each geological unit

Mesh format: [OFF](https://en.wikipedia.org/wiki/OFF_(file_format))

# Dependencies

See [Geocruncher Doc](./geocruncher.md)
