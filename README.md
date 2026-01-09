# Mold Complaints in New York City
This repository contains code to analyze 311 service request data for residential mold among renters in New York City (referred to here as "mold complaints").

# Overview


# Raw data sources

## 311 complaints 
The 311 Service Requests database, 2010-present was downloaded on March 3, 2025 (NYC Open Data, n.d.). The analysis was restricted to data from January 1, 2010 through February 27, 2025 to include only full weeks since January 1, 2010. 

_Citation_ 
- NYC Open Data. (n.d.). 311 Service Requests from 2010 to Present. NYC Open Data. Retrieved March 3, 2025, from https://data.cityofnewyork.us/Social-Services/311-Service-Requests-from-2010-to-Present/erm2-nwe9  

## Tax lot data 
This analysis uses the most recently available NYC MapPLUTO 2025 (Version 25v2) file (NYC Department of City Planning, Information Technology Division, 2025). To capture pre-Hurricane Sandy housing, the analysis also uses the version of the data release immediately prior to Hurricane Sandy in 2012 (Version 12v2) (NYC Department of City Planning, Information Technology Division, 2012). 

_Citations_ 
- NYC Department of City Planning, Information Technology Division. (2012). MapPLUTO - Shoreline Clipped (Version 12v2) [Dataset]. PLUTO, MapPLUTO and PLUTO Change File. https://www.nyc.gov/content/planning/pages/resources/datasets/mappluto-pluto-change 
- NYC Department of City Planning, Information Technology Division. (2025). MapPLUTO - Shoreline Clipped (Version 25v2) [Dataset]. PLUTO, MapPLUTO and PLUTO Change File. https://www.nyc.gov/content/planning/pages/resources/datasets/mappluto-pluto-change  

## Geocoding  
The NYC Geoservices API was used to provide georeferenced coordinates that allowed for a join with the MapPLUTO building footprints. This was done because coordinates from the 311 database yield points that are in the middle of the street and matching based on addresses yielded a low match rate. The NYC Geoservices API yielded points approximately 5 feet within the building footprint and allowed for a spatial join to tax lots from the NYC MapPLUTO shapefile (NYC Department of City Planning, Information Technology Division, 2025), with 99.98% success (complaints with locations but did not intersect with a 2025 tax lot were excluded from further analysis). 

_Citation_ 
- NYC Department of City Planning. (n.d.). Geoservice API Function AP (Version 1.0) [Dataset]. https://geoservice.planning.nyc.gov/FunctionAP  

## Census tracts 
The R package tidycensus (Walker & Herman, 2023) was used to obtain shapefiles with the boundaries of the 2020 Census tracts. Only those within the five boroughs of NYC were included, yielding N = 2327 tracts. Analyses excluded tracts with less than 500 population (~5th percentile) and/or under 100 housing units (~5th percentile) as some tracts had population in 2020 with no housing units from ACS and vice versa. This yielded 2218 tracts. For visualization purposes, the  2020 Census Tracts (Clipped to Shoreline) shapefile from the NYC Department of City Planning was used (NYC Department of City Planning, 2025). 
_Citations_ 
- Walker, K., & Herman, M. (2023). tidycensus: Load US Census Boundary and Attribute Data as “tidyverse” and ’sf’-Ready Data Frames (Version 1.5) [R]. https://walker-data.com/tidycensus/ 
- NYC Department of City Planning. (2025). Census Tracts. https://www.nyc.gov/content/planning/pages/resources/datasets/census-tracts 

## Spatial weights matrix 
For the 2218 tracts included in the analysis, a spatial weights matrix was created using the software GeoDa (version 1.20.0.20) (Anselin et al., 2006). The resulting matrix had three “islands” with zero neighbors: City Island (Bronx Tract 36005051601), Breezy Point/Roxbury Point (Queens Tract 36081091603), and Broad Channel (Queens Tract 36081107201) and two tracts with only one neighbor (Queens Tract 36081003900 and Bronx Tract 36005044901). The spatial weights matrix was edited to add neighbors based on a local understanding of neighborhood connectedness and road networks so that all tracts had at least two neighbors. 

_Citation_ 
- Anselin, L., Syabri, I., & Kho, Y. (2006). GeoDa: An Introduction to Spatial Data Analysis. Geographical Analysis, 38(1), 5–22. https://doi.org/10.1111/j.0016-7363.2005.00671.x 

## Number of public housing units per Census tract 
The Assisted Housing: National and Local dataset from HUD USER Office of Policy Development was downloaded for the most recent available year 2022 by 2020 tracts (HUD USER Office of Policy Development and Research, n.d.). The data were filtered to include only tracts in the 5 boroughs with public housing, defined here as units indicated as “Public Housing” that would report to NYCHA prior to 2022 and excluding other programs such as Section 8 that might have tenants living in private housing, who would report to 311 over the entire time period. The data were filtered to include only tracts in the 5 boroughs with public housing (defined here as units indicated as “Public Housing” that would report to NYCHA prior to 2022 and excluding other programs such as Section 8 that might have tenants living in private housing, who would report to 311) and averaged per tract over the years, yielding 177,009 total units, compared to 160,897 current units listed in a 2022 NYCHA report (New York City Housing Authority Performance Tracking and Analytics Department, 2022). 

_Citations_ 
- HUD USER Office of Policy Development and Research. (n.d.). Assisted Housing: National and Local | HUD USER. Retrieved June 30, 2025, from https://www.huduser.gov/portal/datasets/assthsg.html#codebook_2009-2022  
- New York City Housing Authority Performance Tracking and Analytics Department. (2022). NYCHA Development Data Book 2022. New York City Housing Authority. https://www.nyc.gov/assets/nycha/downloads/pdf/pdb2022.pdf  

## Census data 
Data on demographic and socioeconomic characteristics were obtained from the Decennial Census and American Community Survey using via the R package tidycensus (Walker & Herman, 2023) and the Census API (U.S. Census Bureau, 2022).  

_Citations_
- Walker, K., & Herman, M. (2023). tidycensus: Load US Census Boundary and Attribute Data as “tidyverse” and ’sf’-Ready Data Frames (Version 1.5) [R]. https://walker-data.com/tidycensus/ 
- U.S. Census Bureau, “American Community Survey 5-Year Estimates: Comparison Profiles 5-Year,” 2022, <http://api.census.gov/data/2022/acs/acs5>, accessed on September 21, 2025. 
- United States Census Bureau. (2022). American Community Survey and Puerto Rico Community Survey Design and Methodology Version 3.0. https://www2.census.gov/programs-surveys/acs/methodology/design_and_methodology/2022/acs_design_methodology_report_2022.pdf 

## Weather data (precipitation, temperature, humidity) 
Data obtained from NOAA National Climatic Data Center included the three weather stations located within NYC boundaries (Central Park, JFK, LaGuardia). Data were analyzed on the daily scale to obtain the average precipitation, with sensitivity analyses.

_Citations_
- Menne, M. J., Durre, I., Korzeniewski, B., McNeill, S., Thomas, K., Yin, X., Anthony, S., Ray, R., Vose, R. S., Gleason, B. E., & Houston, T. G. (2012). Global Historical Climatology Network—Daily (GHCN-Daily), Version 3 [Dataset]. NOAA National Centers for Environmental Information. https://doi.org/10.7289/V5D21VHZ 
- Menne, M. J., Durre, I., Vose, R. S., Gleason, B. E., & Houston, T. G. (2012). An Overview of the Global Historical Climatology Network-Daily Database. Journal of Atmospheric and Oceanic Technology, 29(7), 897–910. https://doi.org/10.1175/JTECH-D-11-00103.1 

## Hurricane Sandy Inundation Zone 
Hurricane Sandy inundation zone shapefiles were downloaded from NYC Open Data. 

_Citation_ 
- NYC Open Data. (n.d.). Sandy Inundation Zone. Retrieved May 22, 2023, from https://data.cityofnewyork.us/Environment/Sandy-Inundation-Zone/uyj8-7rv5  
