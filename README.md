
<!-- README.md is generated from README.Rmd. Please edit that file -->
[![Travis-CI Build Status](https://travis-ci.org/boshek/limnotools.svg?branch=master)](https://travis-ci.org/boshek/limnotools) [![AppVeyor Build Status](https://ci.appveyor.com/api/projects/status/github/boshek/limnotools?branch=master&svg=true)](https://ci.appveyor.com/project/boshek/limnotools) [![Coverage Status](https://img.shields.io/codecov/c/github/boshek/limnotools/master.svg)](https://codecov.io/gh/boshek/limnotools?branch=master)

limnotools package
------------------

limnotools is an R package that implements the split and merge algorithm to high resolution water column profile to identify water column structure, stratification and mixing patterns.

See: [limnotools vignette](https://github.com/boshek/limnotools/blob/master/vignettes/limnotools.Rmd)

You can install the development version of limnotools from github with:

``` r
devtools::install_github("boshek/limnotools")
```

Important references
--------------------

-   Pavlidis, T., and S. L. Horowitz, 1974: Segmentation of plan curves.IEEE Trans. Comput., C-23, 860–870.
-   Thomson, R. and I. Fine. 2003. Estimating Mixed Layer Depth from Oceanic Profile Data. Journal of Atmospheric and Oceanic Technology. 20(2), 319-329.
-   Fiedler, Paul C. "Comparison of objective descriptions of the thermocline. Limnology and Oceanography: Methods 8.6 (2010): 313-325.

Releases
--------

-   0.9.0 Initial release
-   0.9.1 Fixed a fatal bug in wtr\_layer and wtr\_segment that fed incorrect measurement values in the call to by\_s\_m and by\_s\_m3
