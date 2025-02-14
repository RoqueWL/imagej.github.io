---
mediawiki: Voxelization
title: Voxelization
section: Imaging
nav-links: true
---

# Voxelization

Voxelization is the process of converting a data structures that store geometric information in a continuous domain (such as a 3D triangular mesh) into a rasterized image (a discrete grid).

## Instructions for voxelizing a 3D mesh in ImageJ

### Requirements

Enabled [ThreeDViewer](/plugins/sciview) update site

### Steps

1.  Launch SciView ![](/media/imaging/launch-threedviewer.png)
2.  Import a 3D mesh (at the time of this writing OBJ, STL, and Isosurfaces taken from a 3D image opened in ImageJ, all work) ![](/media/imaging/import-obj.png)
3.  Convert mesh to image ![](/media/imaging/mesh-to-image.png)
4.  Select output dimensions ![](/media/imaging/mesh-to-image-dimensions.png)
5.  Inspect the result ![](/media/imaging/voxelization-output.png)

### Optional additional steps

This voxelization procedures creates an image that represents the \*surface\* of the mesh. Firstly, it may be possible that the geometry of the surface does not voxelize well at particular resolutions, resulting in gaps in the output image (i.e. the result is not "watertight"). In these cases either try another resolution, or try filling in the gaps with either manual touchup, or image processing routines, such as dilation.

If a filled volume is desired, then take a watertight image (see above) and use the Flood Fill (3D) utility available within [Fiji](/software/fiji) under the {% include bc path='Plugins|Process|Flood Fill (3D)'%}.
