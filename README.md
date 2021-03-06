
<!-- README.md is generated from README.Rmd. Please edit that file -->

# materialmodifier

<!-- badges: start -->

[![CRAN\_Status\_Badge](https://www.r-pkg.org/badges/version/materialmodifier)](https://cran.r-project.org/package=materialmodifier)
[![CRAN\_time\_from\_release](https://www.r-pkg.org/badges/ago/materialmodifier)](https://cran.r-project.org/package=materialmodifier)
[![CRAN\_latest\_release\_date](https://www.r-pkg.org/badges/last-release/materialmodifier)](https://cran.r-project.org/package=materialmodifier)
[![metacran
downloads](https://cranlogs.r-pkg.org/badges/grand-total/materialmodifier)](https://cran.r-project.org/package=materialmodifier)
[![license](https://img.shields.io/github/license/mashape/apistatus.svg)](https://choosealicense.com/licenses/mit/)
<!-- badges: end -->

This is an R package that is used for applying image-based material
editing effects. The user can edit the appearance of objects in an
image, such as enhancing the gloss of fruits and removing the blemishes
of facial skin.

<p>
<img src="notes/summary_tiny.png" width="100%">
</p>

## Paper

Details of this package (e.g. image processing algorithm) will be
described in the article below:

Tsuda and Kawabata (under review). materialmodifier: an R package for
image-based material editing.

## Dependencies

Mac users need to install XQuartz (<https://www.xquartz.org/>).

## Installation

This package is currently not available on CRAN. The development version
of the package can be installed vie GitHub.

### Installation via GitHub

You can install the development version of the `materialmodifier`
package via GitHub, by using the `devtools` package.

``` r
# install the devtools package
install.packages("devtools")
```

**NOTE:**  
To install a package from GitHub,  
- On Windows,
<a href="https://cran.r-project.org/bin/windows/Rtools/">Rtools</a>
needs to be installed.  
- On Mac, XCode may be needed to be installed.

After you have installed the required software stated above, install the
package as follows:

``` r
# install the materialmodifier package
devtools::install_github("tsuda16k/materialmodifier")
```

Then, load the package.

``` r
library(materialmodifier)
```

## Example image

The `materialmodifier` package includes an image data, which is useful
when you want to try material editing effects right away. The variable
name of the image is `face`.

Internally, this is just a numeric array of size 500 x 450 x 3
\[y-coordinate, x-coordinate, color channel\], meaning that it is 500
pix height and 450 pix width, and has three color channels (Red, Green,
and Blue). Each element of the array represents a pixel value, which can
range between 0 and 1.

``` r
dim(face)
#> [1] 500 450   3
```

Image size information can be shown by typing the variable name.

``` r
face
#> face: 500 [height] x 450 [width] x 3 [colour channels]
```

To plot an image, use the `plot()` function.

``` r
plot(face)
```

<img src="notes/face.png" width="40%">

## Load an image

To load an image you like, use the `im_load()` function.

``` r
im = im_load("path/to/your/image.jpg")
plot(im)
```

The jpg, png, and bmp formats are supported.

You can load an image from the web (URL). For example,

``` r
im = im_load("https://raw.githubusercontent.com/tsuda16k/materialmodifier/master/notes/meat.png")
plot(im)
```

will load an image of roasted meat.

<img src="notes/meat.png" width="40%">

## Apply material editing effects

The built-in face image is used in the following examples.  
For consistency purposes, the `face` image is labeled as `im`.

``` r
im = face
```

Use the `modif()` function to apply a material editing effect to an
image.  
(Note: the function name is `modif`, not `modify`)

Below is an example of the `shine` effect.

``` r
# apply the shine effect
im2 = modif(im, effect = "shine", strength = 2.5) # may take some seconds
plot(im2) # see the result
```

<p>
<img src="notes/face.png" width="40%">
<img src="notes/face_shine25.png" width="40%">
</p>

On the left is the input image, and on the right is the result of the
`shine` effect.

The `strength` parameter is used to control the strength of effects.
Larger values of `strength` give stronger effects:

<p>
<img src="notes/face_shine01.png" width="20%"><img src="notes/face.png" width="20%"><img src="notes/face_shine20.png" width="20%"><img src="notes/face_shine30.png" width="20%"><img src="notes/face_shine40.png" width="20%">
</p>

The `strength` parameter is (from left to right) 0.1, 1, 2, 3, and 4.

Note that when the `strength` parameter is 1, the `modif()` function
just returns the input image. Therefore the second image from the left
is the same as the input image.

When the `strength` parameter is between 0 and 1, the output image
becomes less shiny than the original (see the leftmost image above).

## Another example

Below is an example of the `aging` effect.

``` r
# load the face image
im = im_load("https://raw.githubusercontent.com/tsuda16k/materialmodifier/master/notes/meat.png")
# apply the aging effect
im2 = modif(im, "aging", 0.1)
im3 = modif(im, "aging", 2.5)
# show the results
plot(im2)
plot(im)
plot(im3)
```

<p>
<img src="notes/face_aging01.png" width="30%">
<img src="notes/face.png" width="30%">
<img src="notes/face_aging25.png" width="30%">
</p>

Compared to the original (center), skin blemishes are reduced on the
left and boosted on the right.

## Applying multiple effects at the same time

You can apply multiple effects at the same time. To do so, set vectors
instead of scalars to the `effect` and `strength` parameters.

The below is an example of applying the `shine` and `aging` effects
simultaneously.

``` r
# make a face more shiny and less blemished by a single line
im2 = modif(face, effect = c("shine", "aging"), strength = c(2, 0.2))
plot(im2)
```

<p>
<img src="notes/face_shine_aging.png" width="30%">
</p>

Applying each effect in sequence usually produces almost identical
results.

``` r
# Applying effects in sequence
im3 = modif(face, effect = "shine", strength = 2)
im3 = modif(im3, effect = "aging", strength = 0.2)

# pixel-wise mean squared error
mean((im2-im3)^2) # this is very small, meaning that im2 and im3 are almost identical
```

When you apply multiple effects to an image, it is recommended to use
the single-line approach, rather than applying each effect in sequence,
because the former approach is faster.

## List of material editing effects

The `effect` parameter of the `modif()` function is either “gloss”,
“shine”, “spots”, “blemish”, “rough”, “stain”, “shadow”, or “aging”.

The image below illustrates representative outputs of each effect  
(A) List of effects  
(B) Results of the aging effect for different levels of the strength
parameter

<p>
<img src="notes/effect_table_small_tiny.png" width="95%">
</p>

## Effect of parameters: in more detail

I would like to add a set of summary of output images derived from a
combination of `effect` and `strength` parameters by using a single
image as the input.

<p>
<img src="notes/combinations_face.png" width="95%">
</p>
<p>
<img src="notes/combinations_meat.png" width="95%">
</p>
<p>
<img src="notes/combinations_kettle.png" width="95%">
</p>

Note that although setting a negative value to the `strength` parameter
produces weird results in most cases (see the bottom row in each
figure), doing so is sometimes useful to find the region that was
changed by the `modif()` function.

For example, the `rough` and `blemish` effects appear to produce similar
results, but by comparing the outputs of setting a negative value to the
strength parameter (the bottom row in each figure), we can see both
effects are not identical. Technically, the `blemish` effect is
equivalent to applying both `rough` and `stain` effects at the same
time, because HLA (blemish) = HLP (rough) + HLN (stain) (see our paper
for details about the BS feature).

## The modif2() function

The `modif2()` function allows for a precise control over which image
component(s) to manipulate. For example, boosting the `LAA` feature (low
spatial frequency, all (high and low) amplitudes, and all (positive and
negative) signs) can be performed as follows.

``` r
# modify the LAA feature
im2 = modif2(im, params = list(freq = "L", amp = "A", sign = "A", strength = 2 ))
im2 = modif2(im, params = list(feature = "LAA", strength = 2 )) # equivalent to the above
```

Details about image manipulation features (BS features) are described in
our paper.

Here are additional examples for using the `modif2()` function.

``` r
# shine effect (boost the HHP feature)
shine = list(freq = "H", amp = "H", sign = "P", strength = 2)
plot(modif2(face, params = shine))

# shine effect (equivalent to the above)
shine2 = list(freq = 1:4, amp = "H", sign = "P", strength = 2)
plot(modif2(face, params = shine2))

# you can specify a feature name directly, instead of specifying freq/amp/sign separately
plot( modif2(face, params = list(feature = "HHA", strength = 2)) )
plot( modif2(face, params = list(feature = "1HP", strength = 3)) )

# apply multiple effects at the same time
blemish = list(feature = "HLA", strength = 0.1) # less blemished
smooth  = list(feature = "HHN", strength = 0.2) # smoother
plot(modif2(face, params = list(blemish, smooth)))
```

## Parameters of the modif() function

The `modif()` function has 6 arguments.

``` r
modif(im, effect, strength, max_size = 1024, log_epsilon = 0.0001, filter_epsilon = 0.01)
```

Table of arguments of the `modif()` function:

| Argument        | Meaning                                | Value                                                                               | Default |
|:----------------|:---------------------------------------|:------------------------------------------------------------------------------------|:--------|
| im              | Input image                            | an image object                                                                     |         |
| effect          | Effect name                            | Either “gloss”, “shine”, “spots”, “blemish”, “rough”, “stain”, “shadow”, or “aging” |         |
| strength        | Strength of effect                     | a float value or a float vector                                                     |         |
| max\_size       | Image resolution limit                 | an integer                                                                          | 1024    |
| log\_epsilon    | Offset for log transformation          | a float value                                                                       | 0.0001  |
| filter\_epsilon | Epsilon parameter of the Guided filter | a float value                                                                       | 0.01    |

The `im` is an image object we can get by using the `im_load()`
function.  
The `effect` and `strength` parameters have been described above.

The `max_size` parameter can be used to restrict the image resolution.
If the shorter side of the input image is larger than this value (the
default is 1024), input image is resized before applying effects. For
example, when the input image has 1024px x 2048px resolution, and if
`max_size` is 512, then the input image is first resized to 512px x
1024px. Because the modif() function is very slow for large-resolution
images, it is useful to limit the image resolution to speed up the image
processing.

The `log_epsilon` and `filter_epsilon` are parameters that are used for
image processing procedures. You need not to change this value in most
cases.

## Parameters of the modif2() function

The `modif2()` function has 5 arguments.

``` r
modif(im, params, max_size = 1024, log_epsilon = 0.0001, filter_epsilon = 0.01)
```

Table of arguments of the `modif()` function:

| Argument        | Meaning                                | Value           | Default |
|:----------------|:---------------------------------------|:----------------|:--------|
| im              | Input image                            | an image object |         |
| params          | A list of parameter values             | a list          |         |
| max\_size       | Image resolution limit                 | an integer      | 1024    |
| log\_epsilon    | Offset for log transformation          | a float value   | 0.0001  |
| filter\_epsilon | Epsilon parameter of the Guided filter | a float value   | 0.01    |

To the `params` parameter, set a list of material editing parameters:

``` r
# shine effect (boost the HHP feature)
shine = list(freq = "H", amp = "H", sign = "P", strength = 2)
plot(modif2(face, params = shine))

# shine effect (equivalent to the above)
shine2 = list(freq = 1:4, amp = "H", sign = "P", strength = 2)
plot(modif2(face, params = shine2))

# you can specify a feature name directly, instead of specifying freq/amp/sign separately
plot( modif2(face, params = list(feature = "HHA", strength = 2)) )
plot( modif2(face, params = list(feature = "1HP", strength = 3)) )

# apply multiple effects at the same time
blemish = list(feature = "HLA", strength = 0.1) # less blemished
smooth  = list(feature = "HHN", strength = 0.2) # smoother
plot(modif2(face, params = list(blemish, smooth)))
```

The other parameters are the same as the `modif()` function.

## Calculate the BS feature energy

By using the `get_BS_energy()` function, we can calculate the BS feature
energy information of an image.

``` r
# calculate the BS feature energy
en = get_BS_energy(face)
en
#>    feature       energy normalized
#> 1      HHP 0.0012270066 0.10296136
#> 2      HHN 0.0015918547 0.13357672
#> 3      HLP 0.0002768922 0.02323475
#> 4      HLN 0.0001923895 0.01614391
#> 5      LHP 0.0023203040 0.19470283
#> 6      LHN 0.0046927525 0.39378123
#> 7      LLP 0.0010638444 0.08926998
#> 8      LLN 0.0005521125 0.04632921
#> 9    total 0.0119171563 1.00000000
#> 10     HLA 0.0003897122 0.03270177
#> 11     LAN 0.0064410873 0.54048861
#> 12   aging 0.0021860476 0.18343702
```

This function calculates the energy of each BS feature (defined as the
sum of squared magnitude of a given BS feature). The total energy (the
9th row) is the sum of the eight BS feature energies.

Each energy is divided by the total energy to give the normalized energy
measure.

In addition to the eight BS features, this function also calculates the
energy for HLA, LAN, and aging features (10th to 12th rows).
