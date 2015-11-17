# ltutilities
Command line utilities for image processing of LandTrendr.

Utilities included:
- intersectMask
- clipRaster
- addTSAs
- echoKernel
- silentRun

Dependency: LandTrendr lthacks library - https://github.com/LandTrendr/lthacks

Clone somewhere in your $PATH for command line access.

______________________________________________________________________
**intersectMask <source> <mask> <output> [--src_band=<sb>] [--msk_band=<mb>] [--msk_value=<mv>] [--meta=<m>]**

This script masks out pixels in the source file that are 0’s (or not equal to --msk_value indicated) in the mask file and saves it as the output file. Unlike using band math or the masking tool in ENVI, the mask raster does not need to be the same size as the source file. The mask raster also does not need to only be comprised only 0’s and 1’s - this allows for use of tsa no buffer masks in the spatial_data folder on cluster. 

**Updates: --nodata & --datatype options no longer exist. Output will be in same datatype as source. If nodata value is set in source, the same nodata value will be set in output. --msk_value option has been added to give more flexibility of masks that can be used.

Example:

*intersectMask ./disturbance_map.bsq ./forest_mask.bsq ./disturbance_forestsonly.bsq --src_band=2 --meta=”second run”*

This line turns all pixels in band 2 of “disturbance_map.bsq” to 0’s that are 0’s in corresponding coordinates in “forest_mask.bsq” and saves the masked out file as “disturbance_forestsonly.bsq.” “Second run” comment will be added to the meta.txt file.

______________________________________________________________________
**clipRaster <source> <clip> <output> [--poly_band=<pb>] [--nodata=<n>] [--field=<f>] [--attr=<a>...]**

This is a wrapper for the gdal commands ‘gdalwarp’ and ‘gdal_polygonize.’ It clips a source file to the extent of a clip file and saves it as the specified output file. The clip file may be a raster or a shapefile. If the clip file is a raster, it will first be converted into a shapefile and saved with the same filename as the clip file in the same directory as the output file. To indicate a field name for the new shapefile, specify with the ‘--field’ option. To clip only to the extent of specific attributes of the shapefile, use the ‘--field’ and ‘--attr’ options together.

NOTE: crop_to_cutline feature has been removed because pixels were shifted by half-pixel. This may still require additional cropping. Consider using intersectMask instead.

Example:

*clipRaster ./big_map.bsq ./modelregions.shp ./small_map.bsq --nodata=0 --field=MR --attr=224 --attr=227*

This line clips band 1 of raster “big_map.bsq” to the extent of “modelregions.shp” where MR=224 or 227. The output is “small_map.bsq,” where the nodata value is set to 0. 

______________________________________________________________________
**addTSAs <sourceCsv> <outputCsv> <tsa_ref_mask>** 

Inserts TSA column into CSV containing X,Y coordinates. 

______________________________________________________________________
**echoKernel.py <mappath> <x> <y> <kernel_size> [--band=<b>...]**

Prints pixel values to your terminal for specified kernel. Useful for debugging and quick checks.

______________________________________________________________________
**silentRun <command> [--logfile=<lg>]**

Submits a command as a background process on Islay. Redirects stdout & stderr outputs to a log file if specified. 
