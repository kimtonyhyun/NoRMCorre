# NoRMCorre: Non-Rigid Motion Correction 
This package can be used for online piecewise rigid motion correction of 2d (planar) or 3d (volumetric) calcium imaging data. 

## Synopsis

The algorithm operates by splitting the field of view into a set of overlapping patches. For each patch and each frame a rigid translation is estimated by aligning the patch against a template using an efficient, FFT based, algorithm for subpixel registration [1](#ref). The estimated set of translations is further upsampled to a finer resolution to create a smooth motion field that is applied to a set of smaller overlapping patches. Extra care is taken to avoid smearing caused by interpolating overlapping patches with drastically different motion vectors. The registered frame is used to update the template in an online fashion by calculating a running/mean of past registered frames. The pipeline is summarized in the figure below.

![Alt text](dev/pipeline.png?raw=true "piecewise rigid motion correction pipeline")

## Code details

See the function ```demo.m``` for an example of the code. The algorithm is implemented in the function ```normcorre.m```. If you have access to the parallel computing toolbox, then the function ```normcorre_batch.m``` can offer speed gains by enabling within mini-batch parallel processing. The user gives a dataset (either as 3D or 4D tensor loaded in RAM or memory mapped, or a pointer to a .tiff stack or .hdf5 file), and a parameters struct ```options```. Optionally, an initial template can also be given.

The ```options``` struct can be set either manually or by using the function ```NoRMCorreSetParms.m```. The most important parameters of the ```options``` struct are the following:

| Parameter name | Description |
|----------------|-------------|
| ```grid_size``` | size of non-overlapping portion of the grid in each direction (x-y-z)|
| ```overlap_pre```| size of overlapping region in each direction before upsampling  |
| ```mot_uf```    | upsampling factor for smoothing and refinement of motion field |
| ```overlap_post ``` | size of overlapping region in each direction after upsampling |
| ```max_shift``` | maximum allowed shift for rigid translation | 
| ```max_dev``` | maximum deviation of each patch from estimated rigid translation |
| ```upd_template``` | update the template after registering some frames |
| ```bin_width``` | length of bin over which the registered frames are averaged to update the template |
| ```iter``` | numeber of times to go over the dataset |


## Developers

[Eftychios A. Pnevmatikakis](https://github.com/epnev), Flatiron Institure, Simons Foundation

## Integrations 

This package will be integrated with the [Matlab code](https://www.github.com/epnev/ca_source_extraction) for source extraction and deconvolution using CNMF.

A python version of this algorithm developed from [Andrea A. Giovannuci](https://github.com/agiovann) is included as part of the [CaImAn](https://github.com/simonsfoundation/CaImAn) package that provides a complete pipeline for calcium imaging data pre-processing.

## Acknowledgements

Example dataset is kindly provided from Andrea Giovannucci, taken at Wang lab (Princeton University).

## References 

<a name="ref"></a>[1] Guizar-Sicairos, M., Thurman, S. T., & Fienup, J. R. (2008). Efficient subpixel image registration algorithms. Optics letters, 33(2), 156-158. Matlab implementation available [here](https://www.mathworks.com/matlabcentral/fileexchange/18401-efficient-subpixel-image-registration-by-cross-correlation).
