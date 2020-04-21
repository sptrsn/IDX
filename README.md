
<!-- README.md is generated from README.Rmd. Please edit that file -->
IDX
================

This is the beggining of an RStudio package to fetch and analyze RE data. 
as of 4/21/2020, the getDeepSearchResults has been optimized to loop through fetched data.
An array of Zillow API keys are rolled on each call. 
Failures are stored in as separate data frame. Basic.

Install
------------

``` r

# 0.1 Install ----
library(devtools)
devtools::install_github("sptrsn/IDX",force = TRUE)
```

How To
----------

```r

# 1.0 LIBRARIES ----
library(IDX)

# unsure how many of these libs are actually required
# Manage configurations
library(config)

# EDA
library(DataExplorer)
library(skimr)

# Modeling
library(recipes)
library(parsnip)
library(yardstick)
library(DALEX)
library(iBreakDown)

# Core
library(tidyverse)
library(tidyquant)
library(lubridate)
library(leaflet)
library(plotly)
library(dplyr)

# 2.0 Zillow API Keys ----
keys <- c(
    'X1-ZWz1fvtg2hum8b_9zlkj',
    'X1-ZWz1bw4isfzuob_8uv2u'
    )

# 3.0 Fetch properties ---- 
properties <- read.csv("~/rStudio/zillowtape.csv",header=TRUE)

# 3.1 create empty containers for Zillow & error data ----
zillowData = data.frame()
failed = data.frame()

# 3.2 create counter to track key usage ----
keycounter <- 1

# 4.0 Loop properties ----
for(i in 1:nrow(properties)){
 
    tryCatch(
        {
                
            # 4.1 Modify col (2,3,4,5) to match address, city, state & zip in your data ----
            address <-as.character(properties[i,2])
            city    <-as.character(properties[i,3])
            state   <-as.character(properties[i,4])
            zipcode <-properties[i,5]
            
            # 4.2 call zillow api for property i ----
            response <- GetDeepSearchResults(
                address = address, 
                city    = city,
                state   = state,
                zipcode = zipcode,
                rentzestimate = TRUE,
                api_key       = keys[keycounter]
            ) 
           
             # 4.3 increment | reset api key ----
            if(keycounter == length(keys)){ 
                keycounter <- 1
            }else{
                keycounter <- keycounter + 1
            }
            
            if(length(response)==1){
                
                fail<-c(address,zipcode,city,state)
                failed <- rbind(failed,fail)
                
                next
                
            }else{
                
                row<-response
            }
            
            # apend zillow response to the zillowData container
            zillowData <- rbind(zillowData,row)
        },
        error=function(e){
            print("ERROR :",conditionMessage(e), "\n")
        }
    )
    
}# !End for loop


# 5.0 View final zillowData output ----
View(zillowData)


# 6.0 display properties if errors ----
if(nrow(failed) == 0){
    
    print("No Errors")
    
}else{
    
    print("Failed properties being displayed")
    fieldNames<-c("address","zipcode","city","state")
    names(failed)<-fieldNames
    View(failed)
    
}


# 7.0 End ----

# Fields returned by getDeepSearchResults

#fieldNames<-c("address","zipcode","city","state","lat","long","region_name","region_id","type","zestimate","zest_lastupdated","zest_monthlychange","zest_percentile","zestimate_low","zestimate_high","rentzestimate","rent_lastupdated","rent_monthlychange","rentzestimate_low","rentzestimate_high","zpid","bathrooms","bedrooms","finishedSqFt","lastSoldDate","lastSoldPrice","lotSizeSqFt","taxAssessment","taxAssessmentYear","totalRooms","yearBuilt")



```
Original Source
------
This is a fork from https://github.com/xiyuansun/realEstAnalytics


Zillow
-------

All calls to the API require a unique Zillow API key, which you can acquire by signing up at <https://www.zillow.com/howto/api/APIOverview.htm> .

