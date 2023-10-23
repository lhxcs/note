# Lab1 Image Formation

!!! abstract

    In this exercise, you will gain hands-on experience regarding the image formation process and geometric transformations.

## Perspective Projection

### a

Implement the function `get camera intrinsics` which takes as input the focal lengths `fx, fy` and the principal point $(c_x, c_y)^T$  and returns a 3 *×* 3 camera intrinsics matrix **K**.



### b

Implement the function `get perspective projection` which takes as input a 3D point in camera space $x_c$ and the camera matrix **K** and it returns a two-dimensional point in screen space, hence the pixel coordinates $x_s$ for the 3D point $x_c$.



### c

Implement the function `get face color` which maps a normal vector **n** and a point light direction vector **r** to an RGB color value. We assume the light to be a point light source, i.e., the light source is infinitely small. You should follow the rendering equation discussed in the lecture where you can assume that the surface does not emit light, the BRDF term is always 1, and the incoming light $L$ in is 1 for the point light source direction.



### d

Create a visualization showing the Dolly Zoom Effect. For this, linearly change the focal lengths between [10*,* 150] while you also translate the cube along the z-axis between [0*.*9*,* 5]



## Orthographic Projection

Implement the function `get orthographic projection` which maps a 3D point in camera space $x_c$ to 2D pixel coordinates $x_s$ using a orthographic projection.





## Panorama Stitching using DLT

### a

Implement the function `get_Ai` which takes as input the two homogeneous points $\tilde{x}_i$ and $\tilde{x}_i^{'}$ and returns the 2 *×* 9 matrix $A_i$ discussed in the lecture. (Note: It’s 2 *×* 9 and not 3 *×* 9 as you can drop the last row (see lecture).)



### b

Implement the function `get A` which uses the `get Ai `for all *N* correspondence pairs and concatenates them to return the 2*N* *×* 9 matrix **A**.



### c

Implement the function `get homography` which maps the *N* point correspondences to the homography **H** using the Direct Linear Transform (DLT) algorithm.