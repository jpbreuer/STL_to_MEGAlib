# STL_to_MEGAlib

Without support of the CADMesh library, MEGAlib is not yet able to import and use CAD models or complex geometry directly. As such, MEGAlib simulations tend to use simplified mass models to approximate these more complex geometries.

This project is a python code that takes a complex 3D CAD model (from .stl) and creates a MEGAlib compatible .geo file, that is then usable directly in simulations.

## Method
Starting with the .stl file, which describes the 3D CAD geometry as a series of triangles (edges and vertices), we first integrate the over the volume and convert the triangles into a series of cubes (voxels). 

The number of total cubes required to describe the CAD model is exponentially increasing, and is dependent on the requested number of slices (resolution). The higher the resolution, the smaller the voxels, and the more detail will be present in the MEGAlib geometry. This results in larger file sizes, and increased RAM requirements for simulations.

Before creating the output .geo file, we attempt various methods of binning and grouping the adjacent voxels together so that we can optimise a bit and reduce the file size and total number of required voxels.

The current binning implementation was able to reduce a complex geometry from tens of millions of voxels, down to a few thousand BRIKs.

Please feel free to update, add, or change the binning routines as you like.

## Example usage
This is skipping the imports, and the function definitions, but you should be able to follow.

```
stl_file = "~/Scripts/geant4_grbalpha/camelot/base_v3_ascii.stl"

resolution=512
voxel_size=None
parallel=False

new_obj = Convert_STL_to_voxels(stl_file=stl_file, resolution=resolution, voxel_size=voxel_size, parallel=parallel)
vox, scale, shift, dim_cm, vox_cm, dim_mm, vox_mm = new_obj.convert_file()

bricks = voxel_array_to_bricks(vox)
write_bricks_to_geo(bricks,
                    voxel_size=vox_cm,          # mm or cm as you prefer
                    shift=shift,                 # same shift you used earlier
                    outfile="model_bricked.geo")
```

