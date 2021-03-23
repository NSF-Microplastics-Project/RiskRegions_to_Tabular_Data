---
title: "Assigning Risk Region Column to Tabular Data"
author: "Emma Sharpe"
date: "3/23/2021"
output: 
 html_document:
    code_download: true
    code_folding: hide
    df_print: paged
    keep_md: true
    toc: true
    toc_float: true
    theme: flatly
    highlight: tango
---
# Load packages

```r
library(tidyverse)
```

```
## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.0 ──
```

```
## ✓ ggplot2 3.3.3     ✓ purrr   0.3.4
## ✓ tibble  3.1.0     ✓ dplyr   1.0.5
## ✓ tidyr   1.1.3     ✓ stringr 1.4.0
## ✓ readr   1.4.0     ✓ forcats 0.5.1
```

```
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
```

```r
library(sf)
```

```
## Linking to GEOS 3.8.1, GDAL 3.1.4, PROJ 6.3.1
```

```r
library(here)
```

```
## here() starts at /Users/emmasharpe/Documents/B. R Studio /Projects/GitHub Repos/RiskRegions_to_Tabular_Data
```

```r
library(readxl)
library(IETC) # To load packages use > devtools::install_github("WWU-IETC-R-Collab/IETC")
```

# Load Risk Region Shapefile

```r
# Unzip the shapefile from the GitHub repository "Risk_Region.shapefile"
unzipShape("https://github.com/NSF-Microplastics-Project/Risk_Region.shapefile/raw/main/Data/SFB_RiskRegions_20210304_SP.zip")
```

```
## Reading layer `SFB_RiskRegions_20210304_SP' from data source `/private/var/folders/y9/fm2hb65j7_gf39v8djx1_qyc0000gn/T/Rtmp0Qg5Gt/file131b115ee8096/SFB_RiskRegions_20210304_SP.shp' using driver `ESRI Shapefile'
## Simple feature collection with 4 features and 5 fields
## Geometry type: POLYGON
## Dimension:     XY
## Bounding box:  xmin: 1802335 ymin: 557943.6 xmax: 1921947 ymax: 742529.4
## Projected CRS: NAD83(2011) / California zone 3
```

```r
# The code below is not related to above but is indicating the zipfolder in this working directory. I am taking out the .shp to be use when cutting the data. 

PROJECT.riskregions <- st_read("SFB_RiskRegions_20210304_SP (1)/SFB_RiskRegions_20210304_SP.shp")
```

```
## Reading layer `SFB_RiskRegions_20210304_SP' from data source `/Users/emmasharpe/Documents/B. R Studio /Projects/GitHub Repos/RiskRegions_to_Tabular_Data/SFB_RiskRegions_20210304_SP (1)/SFB_RiskRegions_20210304_SP.shp' using driver `ESRI Shapefile'
## Simple feature collection with 4 features and 5 fields
## Geometry type: POLYGON
## Dimension:     XY
## Bounding box:  xmin: 1802335 ymin: 557943.6 xmax: 1921947 ymax: 742529.4
## Projected CRS: NAD83(2011) / California zone 3
```

# Load Tabular Data {.tabset}

## From Github

```r
# Load tabular data
particles <- IETC::unzipShape("https://github.com/WWU-IETC-R-Collab/ClipToProjectBoundary/raw/main/SFEI.particles.locations.zip")
```

```
## Reading layer `SFEI.ID.particles' from data source `/private/var/folders/y9/fm2hb65j7_gf39v8djx1_qyc0000gn/T/Rtmp0Qg5Gt/file131b1115c2030/SFEI.ID.particles.shp' using driver `ESRI Shapefile'
## Simple feature collection with 43554 features and 12 fields
## Geometry type: POINT
## Dimension:     XY
## Bounding box:  xmin: -123.4973 ymin: 37.37356 xmax: -121.91 ymax: 38.2057
## Geodetic CRS:  WGS 84
```

## From Excel Document

```r
### If using an excel sheet ###

# crs.WGS84 <- st_crs(PROJECT.riskregions) # Store WGS84 CRS for defining CRS of tabular data

### Excel Data to sf Object: needs x, y, CRS

# TABULAR_DATA.sf <- TABULAR_DATA %>% 
# st_as_sf(coords=c("LONGITUDE_COLUMN_NAME", "LATITUDE_COLUMN_NAME"), crs = crs.WGS84)
```

# Checking Projection

```r
st_crs(particles)
```

```
## Coordinate Reference System:
##   User input: WGS 84 
##   wkt:
## GEOGCRS["WGS 84",
##     DATUM["World Geodetic System 1984",
##         ELLIPSOID["WGS 84",6378137,298.257223563,
##             LENGTHUNIT["metre",1]]],
##     PRIMEM["Greenwich",0,
##         ANGLEUNIT["degree",0.0174532925199433]],
##     CS[ellipsoidal,2],
##         AXIS["latitude",north,
##             ORDER[1],
##             ANGLEUNIT["degree",0.0174532925199433]],
##         AXIS["longitude",east,
##             ORDER[2],
##             ANGLEUNIT["degree",0.0174532925199433]],
##     ID["EPSG",4326]]
```

```r
st_crs(PROJECT.riskregions) # Check CRS, should match tabular data
```

```
## Coordinate Reference System:
##   User input: NAD83(2011) / California zone 3 
##   wkt:
## PROJCRS["NAD83(2011) / California zone 3",
##     BASEGEOGCRS["NAD83(2011)",
##         DATUM["NAD83 (National Spatial Reference System 2011)",
##             ELLIPSOID["GRS 1980",6378137,298.257222101,
##                 LENGTHUNIT["metre",1]]],
##         PRIMEM["Greenwich",0,
##             ANGLEUNIT["degree",0.0174532925199433]],
##         ID["EPSG",6318]],
##     CONVERSION["SPCS83 California zone 3 (meters)",
##         METHOD["Lambert Conic Conformal (2SP)",
##             ID["EPSG",9802]],
##         PARAMETER["Latitude of false origin",36.5,
##             ANGLEUNIT["degree",0.0174532925199433],
##             ID["EPSG",8821]],
##         PARAMETER["Longitude of false origin",-120.5,
##             ANGLEUNIT["degree",0.0174532925199433],
##             ID["EPSG",8822]],
##         PARAMETER["Latitude of 1st standard parallel",38.4333333333333,
##             ANGLEUNIT["degree",0.0174532925199433],
##             ID["EPSG",8823]],
##         PARAMETER["Latitude of 2nd standard parallel",37.0666666666667,
##             ANGLEUNIT["degree",0.0174532925199433],
##             ID["EPSG",8824]],
##         PARAMETER["Easting at false origin",2000000,
##             LENGTHUNIT["metre",1],
##             ID["EPSG",8826]],
##         PARAMETER["Northing at false origin",500000,
##             LENGTHUNIT["metre",1],
##             ID["EPSG",8827]]],
##     CS[Cartesian,2],
##         AXIS["easting (X)",east,
##             ORDER[1],
##             LENGTHUNIT["metre",1]],
##         AXIS["northing (Y)",north,
##             ORDER[2],
##             LENGTHUNIT["metre",1]],
##     USAGE[
##         SCOPE["unknown"],
##         AREA["USA - California - SPCS - 3"],
##         BBOX[36.73,-123.02,38.71,-117.83]],
##     ID["EPSG",6419]]
```

If projections dont match this code with transform the risk region shapefile projection to be the same as the projection for the tabular data 

```r
PROJECT.riskregions <- st_read("SFB_RiskRegions_20210304_SP (1)/SFB_RiskRegions_20210304_SP.shp") %>% # transforms shapefile CRS to WGS84 vs NAD83
  st_transform(st_crs(particles))
```

```
## Reading layer `SFB_RiskRegions_20210304_SP' from data source `/Users/emmasharpe/Documents/B. R Studio /Projects/GitHub Repos/RiskRegions_to_Tabular_Data/SFB_RiskRegions_20210304_SP (1)/SFB_RiskRegions_20210304_SP.shp' using driver `ESRI Shapefile'
## Simple feature collection with 4 features and 5 fields
## Geometry type: POLYGON
## Dimension:     XY
## Bounding box:  xmin: 1802335 ymin: 557943.6 xmax: 1921947 ymax: 742529.4
## Projected CRS: NAD83(2011) / California zone 3
```

# Join Data
Joins data by name of risk region based on geographical data 

```r
# Join Excel Data to Risk Regions
Tab.Data.RiskRegions <- st_join(particles, PROJECT.riskregions["name"])
```

```
## although coordinates are longitude/latitude, st_intersects assumes that they are planar
## although coordinates are longitude/latitude, st_intersects assumes that they are planar
```
