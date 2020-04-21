
<!-- README.md is generated from README.Rmd. Please edit that file -->
IDX
================

This is the beggining of an RStudio package to fetch and analyze RE data. 
as of 4/21/2020, the getDeepSearchResults has been optimized to loop through fetched data.
An array of Zillow API keys are rolled on each call. 
Failures are stored in as separate data frame. Basic.

Installation
------------

You can install the released version of IDX from [Github](https://github.com) with:

``` r
library(devtools)
devtools::install_github("sptrsn/IDX")
```

Example
-------

All calls to the API require a unique Zillow API key, which you can acquire by signing up at <https://www.zillow.com/howto/api/APIOverview.htm> .



``` r
library(IDX)

# comma sep list of keys.ZWS_ID

keys <- c(
    'X1-ZWz1fvtg2hum8b_9zlkj',
    'X1-ZWz1bw4isfzuob_8uv2u'
    )

```

Calling the Zillow API from R
-----------------------------

Fetch your properties...
```r
properties <- read.csv("~/rStudio/zillowtape.csv",header=TRUE)
```

Use the `GetComps` or `GetDeepComps` to get comparable properties for a given Zillow Property ID (limit 25 comparables). The return is a data frame with the most comparable addresses and their Zestimate values, with more property information (i.e., the `GetDeepSearchResults` variables) available from `GetDeepComps`.


