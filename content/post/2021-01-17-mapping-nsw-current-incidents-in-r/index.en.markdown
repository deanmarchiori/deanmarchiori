---
title: Mapping NSW Fire Incidents in R
author: Dean Marchiori
date: '2021-01-17'
slug: mapping-nsw-current-incidents-in-r
categories: []
tags:
  - R
  - Maps
subtitle: ''
summary: 'How to retieve and map NSW Rural Fire Service current incident feed in R'
authors: []
lastmod: '2022-01-17T08:47:46+11:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---

## Current Incidents Feed (GeoJSON)  

This feed contains a list of current incidents from the NSW RFS, and includes location data and Major Fire Update summary information where available. Click through from the feed to the NSW RFS website for full details of the update.   

GeoJSON is a lightweight data standard that has emerged to support the sharing of information with location or geospatial data. It is widely supported by modern applications and mobile devices.

See here: https://www.rfs.nsw.gov.au/news-and-media/stay-up-to-date/feeds for attribution and guidelines. Please read these important guidelines before using this data.  

##  Code  

Load packages  


```r
library(sf)
library(mapview)
library(tidyverse)
```

Pull incidents  


```r
url <- "http://www.rfs.nsw.gov.au/feeds/majorIncidents.json"
fires <- st_read(url)
```

```
## Reading layer `majorIncidents' from data source 
##   `http://www.rfs.nsw.gov.au/feeds/majorIncidents.json' using driver `GeoJSON'
## Simple feature collection with 16 features and 7 fields
## Geometry type: GEOMETRY
## Dimension:     XY
## Bounding box:  xmin: 141.4329 ymin: -35.86819 xmax: 152.5266 ymax: -30.95235
## Geodetic CRS:  WGS 84
```

Optional step to get points only  


```r
# points only
fire_pt <- fires %>% 
  st_cast("POINT") 
```

Optional Step to get Polygons only. Note the hack to aply a zero distance 
buffer.  


```r
#' Polygons only
fire_poly <- fires %>% 
  st_buffer(dist = 0) %>% 
  st_union(by_feature = TRUE)
```

Mapping data interactively 


```r
mapview(fire_poly, layer.name = "RFS Current Incident Polygons", zcol = "category") +
  mapview(fire_pt, layer.name = "RFS Current Incident Locations", zcol = "category")
```

![screenshot of leafet interactive map of fire incidents](images/fires.png)
