# RGBA Structural Similarity

This tool computes (dis)similarity between two or more PNG images using an algorithm approximating human vision.

Comparison is done using [the SSIM algorithm](https://ece.uwaterloo.ca/~z70wang/research/ssim/) (based on [Rabah Mehdi's implementation](http://mehdi.rabah.free.fr/SSIM/)) at multiple weighed resolutions.

The value returned is 1/SSIM-1, where 0 means identical image, and >0 (unbounded) is amount of difference. Values are not directly comparable with other tools. [See below](#interpreting-the-values) on interpreting the values.

## Features

* Comparison is done in in L\*a\*b\* color space (D65 white point, sRGB gamma) with chroma subsampling. Other implementations use "RGB" or grayscale without gamma correction.
* Supports alpha channel.
* No OpenCV or MATLAB needed.
   - DSSIM version 1.x uses C (C99) and `libpng` or Cocoa on OS X.
   - DSSIM version 2.x is easy to build with [Rust](https://www.rust-lang.org/).

## Usage

    dssim file-original.png file-modified.png

Will output something like "0.02341" (smaller is better) followed by a filename.

You can supply multiple filenames to compare them all with the first file:

    dssim file.png modified1.png modified2.png modified3.png

You can save an image visualising the difference between the files:

    dssim -o difference.png file.png file-modified.png

The `dssim.c` file is also usable as a C library.

### Interpreting the values

The amount of difference goes from 0 to infinity. It's not a percentage.

If you're comparing two different image compression codecs, then ensure you either:

* compress images to the same file size, and then use DSSIM to compare which one is closests to the original, or
* compress images to the same DSSIM value, and compare file sizes to see how much file size gain each option gives.

[More about benchmarking image compression](https://pornel.net/faircomparison).

## Build or Download

You need libpng, zlib, pkg-config and make

    make

Will give you `dssim`. On OS X `make USE_COCOA=1` will compile without libpng.

You'll find [downloads on GitHub releases page](https://github.com/pornel/dssim/releases).

Debian packages for i386/amd64 can be installed for ubuntu (14.04 LTS) from ppa:
```bash
$ sudo apt-add-repository ppa:lkwg82/dssim
$ sudo apt-get update
$ sudo apt-get install dssim
```
(other versions on request)

Plain download from here: https://launchpad.net/~lkwg82/+archive/ubuntu/dssim/+packages

## Accuracy

Scores for version 2.0 measured against [TID2008][1] database:

TID2008 Category | Spearman correlation
--- | ---
Noise   | -0.876
Noise2  | -0.900
Safe    | -0.891
Hard    | -0.904
Simple  | -0.920
Exotic  | -0.504
Exotic2 | -0.664
Full    | -0.827

[1]: http://www.computervisiononline.com/dataset/tid2008-tampere-image-database-2008
