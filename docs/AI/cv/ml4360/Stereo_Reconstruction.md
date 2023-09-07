## Preliminaries

*How to bring images in a suitable configuration such that matching is fast?*
*How to obtain depth from the actual measurements(so called disparities)?*

> How to recover 3D from an image?
> occlusion, parallax, perspective, accomodation, stereopsis……

**Why Binocular Stereopsis?**

- a minimal configuration to percieve depth relatively robustly

### Two-View Stereo Matching

*Goal: Recovering the disparity for every pixel from the input images*
> The *disparity* defined as the relative displacement between pixels in the two images

*Task: Construct a dense 3D model from two images of a static scene*

#### Pipeline

1.Calibrate cameras intrinsically and extrinsically
2.Rectify images given the calibration
3.Compute disparity map for reference image
4.Remove outliers using consistency/occulation test
5.Obtain depth from disparity using camera calibration
6.Construct 3D model

## Image Rectification
