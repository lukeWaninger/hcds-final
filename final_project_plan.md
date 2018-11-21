# Final Project Plan - Wildfire Cause Prediction and Analysis
Luke Waninger

DATA 512 Human-Centered Data Science

University of Washington, Fall 2018


## Introduction
Wildfires have been a big topic in the recent news with devasting effects across the western coast of the United State. So far this year, we've had less burn than 2017 but the current fire in California is the largest in state history and still burns rapidly. Last year, we had almost 2 billion dollars of losses across the United States as a result of wildfire damage; the most in history. The risks of wildfires continue to climb as scientists discover linkage between the two. N. P. Gillett et. al. performed a comprehensive study on various effects showing an positive trend in wildfire burn area is strongly correlated with a positive trend in temperature and greenhouse gasses during the fire season in British Columbia. 

Key to understanding the overall problem is the double edged sword that forests play in climate change. They are both a cause and effect of climate change. The burns put an alarming amount of smoke in the air that both increase atmospheric greenhouse gasses and pollutes the air we breathe while the trees themselves play a vital role in the carbon cycle. The Paris Agreement has specifically mentioned the importance of this and insists that countries protect against deforestation. Not only is the world at large pushing to keep the forests we have but here at home we've began to employ them as major combatants in the fight against climate change. California has led the way with their proposed carbon plan. It proposes methods to reshape parts of their existing ecosystem to take make their forests even more efficient at removing carbon.

But this won't work if they keep burning. The goal of this project is two fold. One, to understand the independent variables and correlation effects in a combined dataset of the Fire Program Analysis (FPA) reporting system, NOAAs Global Surface Summary of Day Data (GSOD) 7, and  NASA's biomass indicators. And two, to train and assess a model for predicting the cause (and possibly impact?) of a wildfire. Identifying the source of a wildfire is a difficult task for investigators. Developing an understanding of the independent variables could help local authorities identify neighborhoods and other land areas with higher risk of fire danger. And, developing a prediction model could give valuable insights to direct causal investigations.

## Data
Three data sources will be used for this project. The main data source was found through Kaggle and contains 1.88 million wildfires that occured in the US. The original data was curated by United States Department of Agriculture ([Forest Service](https://www.fs.fed.us/)) and can be found at [link](https://www.fs.usda.gov/rds/archive/Product/RDS-2013-0009.4/). The second is the GSOD data curated by [NOAA](https://www.noaa.gov/). And finally, the National Air and Space Association hosts a dataset with valuable biome information at the ORNL Distributed Active Archive Center for Biogeochemical Dynamics ([DAAC](https://daac.ornl.gov/NPP/guides/NPP_EMDI.html)).

### USDA Forest Service
Detailed metadata for the dataset can be found at - [Spatial wildfire occurrence data for the United States, 1992-2015](https://www.fs.usda.gov/rds/archive/products/RDS-2013-0009.4/_metadata_RDS-2013-0009.4.html). The following table shows a data dictionary of the independent variables I will be using for the analysis and learning. This list does not consitute all variables in the original dataset but the variables I will be using. The descriptions were pasted from the original defined in the [metadata](https://www.fs.usda.gov/rds/archive/products/RDS-2013-0009.4/_metadata_RDS-2013-0009.4.html)

| Variable Name    | Data Type | Description |
|------------------|-----------|-------------|
| FIRE_CODE        | str       | Code used within the interagency wildland fire community to track and compile cost information for emergency fire suppression (https://www.firecode.gov/).                                                                                                               |
| FIRE_YEAR        | int       | Calendar year in which the fire was discovered or confirmed to exist.                                                                                                                                                                                                    |
| DISCOVERY_DATE   | float     | Date on which the fire was discovered or confirmed to exist.                                                                                                                                                                                                             |
| DISCOVERY_DOY    | int       | Day of year on which the fire was discovered or confirmed to exist.                                                                                                                                                                                                      |
| DISCOVERY_TIME   | str       | Time of day that the fire was discovered or confirmed to exist.                                                                                                                                                                                                          |
| STAT_CAUSE_CODE  | float     | Code for the (statistical) cause of the fire.                                                                                                                                                                                                                            |
| STAT_CAUSE_DESCR | str       | Description of the (statistical) cause of the fire.                                                                                                                                                                                                                      |
| CONT_DATE        | float     | Date on which the fire was declared contained or otherwise controlled (mm/dd/yyyy where mm=month, dd=day, and yyyy=year).                                                                                                                                                |
| CONT_DOY         | int       | Day of year on which the fire was declared contained or otherwise controlled.                                                                                                                                                                                            |
| CONT_TIME        | str       | Time of day that the fire was declared contained or otherwise controlled (hhmm where hh=hour, mm=minutes).                                                                                                                                                               |
| FIRE_SIZE        | float     | Estimate of acres within the final perimeter of the fire.                                                                                                                                                                                                                |
| FIRE_SIZE_CLASS  | str       | Code for fire size based on the number of acres within the final fire perimeter expenditures (A=greater than 0 but less than or equal to 0.25 acres, B=0.26-9.9 acres, C=10.0-99.9 acres, D=100-299 acres, E=300 to 999 acres, F=1000 to 4999 acres, and G=5000+ acres). |
| LATITUDE         | float     | Latitude (NAD83) for point location of the fire (decimal degrees).                                                                                                                                                                                                       |
| LONGITUDE        | float     | Longitude (NAD83) for point location of the fire (decimal degrees).                                                                                                                                                                                                      |
| OWNER_CODE       | float     | Code for primary owner or entity responsible for managing the land at the point of origin of the fire at the time of the incident.                                                                                                                                       |
| OWNER_DESCR      | str       | Name of primary owner or entity responsible for managing the land at the point of origin of the fire at the time of the incident.                                                                                                                                        |
| STATE            | str       | Two-letter alphabetic code for the state in which the fire burned (or originated), based on the nominal designation in the fire report.                                                                                                                                  |
| COUNTY           | str       | County, or equivalent, in which the fire burned (or originated), based on nominal designation in the fire report.                                                                                                                                                        |

#### Limitations
* The database was not curated from a single federal source. The records were aggregated from a disparate set of local agencies, reports, and other sources. Not only are there many different original sources but these sources vary over time. See the [FPA_FOD_Source_List](#target) for more information.
* The dataset does not include elevation, a potential valuable potential indicator as oxygen levels, a key fuel for wildfires, vary by altitude.
* Latitude and longitudes are not records in decimal-degree format and will require conversion.

#### License
> These data were collected using funding from the U.S. Government and can be used without additional permissions or fees. If you use these data in a publication, presentation, or other research product please use the following citation:
> Short, Karen C. 2017. Spatial wildfire occurrence data for the United States, 1992-2015 [FPA_FOD_20170508]. 4th Edition. Fort Collins, CO: Forest Service Research Data Archive. https://doi.org/10.2737/RDS-2013-0009.4

### NOAA GSOD
The data were gathered by the World Meteorologic Organziation (WMO) World WEather Watch Program and finally curated and released by NOAA. It contains over 9000 weather stations around the world but I'll obviously be filtering those to the US (and maybe Canada?). The goal of this dataset will be to engineer features of weekly, 3-day, and 1-day weather aggregations from weather stations just prior to the start of a fire. Performing these aggregations will be a heavy computation burden as there 1.8 million fires. The data records back as far as 1911 but I will exclude any outside the fire date range (< 1992).

| Variable Name | Data Type | Type                                                                                            |
|---------------|-----------|-------------------------------------------------------------------------------------------------|
| STN           | int       | Station number (WMO/DATSAV3 number) for the location                                       |
| WBAN          | int       | WGAN number                                                                                    |
| YEAR          | int       | The year                                                                                       |
| MODA          | int       | The month and day                                                                              |
| TEMP          | float     | Mean temperature for the day in degrees Fahrenheit to tenths                                  |
| DEWP          | float     | Mean dew point for the day in degrees Fahrenheit to tenths                                     |
| SLP           | float     | Mean sea level pressure for the day in millibars to tenths                                     |
| STP           | float     | Mean station pressure for the day in millibars to tenths                                       |
| VISIB         | float     | Mean visibility for the day in miles to tenths                                                 |
| WDSP          | float     | Mean wind speed for the day in knots to tenths                                                 |
| MXSPD         | float     | Maximum sustained wind speed reported for the day in knots to tenths                           |
| GUST          | float     | Maximum wind gust reported for the day in knots to tenths                                      |
| MAX           | float     | Maximum temperature reported during the day in Fahrenheit to tenths                            |
| MIN           | float     | Minimum temperature reported during the day in Fahrenheit to tenths                            |
| PRCP          | float     | Total precipitation (rain and/or melted snow) reported during the day in inches and hundredths |
| SNDP          | float     | Snow depth in inches to tenths--last report for the day if reported more than once             |
| FRSHTT        | int       | Indicators (1=yes|0=no/not reported) indicates catostrophic level storm                         |

#### Limitation
A limitation noted by NOAA is up to a 2 day delay in reporting weather aggregations although this does not effect the current project.

#### License - IS THIS OKAY?
NOAA and WMO provide the data for 'free and unrestricted use in research, education, and other non-commerical activites.'
> The data summaries provided here are based on data exchanged under the World Meteorological Organization (WMO) World Weather Watch Program according to WMO Resolution 40 (Cg-XII).  This allows WMO member countries to place restrictions on the use or re-export of their data for commercial purposes outside of the receiving country.  Data for selected countries may, at times, not be available through this system.   

> Those countries' data summaries and products which are available here are intended for free and unrestricted use in research, education, and other non-commercial activities.  However, for non-U.S. locations' data, the data or any derived product shall not be provided to other users or be used for the re-export of commercial services.  To determine off-line availability of any country's data, please contact --ncdc.orders@noaa.gov,
828-271-4800.  Please email ncdc.info@noaa.gov if you have any other questions.

### ORNL DAAC
This dataset contains a wealth of information that can be used for modeling. For the purposes of this project I will be using two tables that give us vegetation and soil content for a give latitude and longitude.

#### EMDI_ClassA_Soil_IGBP_81.csv
| Variable Name | Data Type | Description                                          | Units           |
|---------------|-----------|------------------------------------------------------|-----------------|
| SITE_ID       | int       | Site identification number based on unique lat/long  |                 |
| LAT_DD        | float     | Latitude of NPP site (point)                         | Decimal degrees |
| LONG_DD       | float     | Longitude of NPP site (point)                        | Decimal degrees |
| SAND          | float     | Sand content in top 30 cm                            | % w/w           |
| SILT          | float     | Silt content in top 30 cm                            | % w/w           |
| CLAY          | float     | Clay content in top 30 cm                            | % w/w           |
| SOILN30       | float     | Soil nitrogen in top 30 cm                           | kg/m2           |
| SOILN20       | float     | Soil nitrogen in top 20 cm                           | kg/m2           |
| SOILN100      | float     | Soil nitrogen in top 100 cm                          | kg/m2           |
| SOILC30       | float     | Soil carbon in top 30 cm                             | g/m2            |
| SOILC20       | float     | Soil carbon in top 20 cm                             | g/m2            |
| SOILC100      | float     | Soil carbon in top 100 cm                            | g/m2            |
| PH            | float     | Soil pH (water) in top 30 cm                         | units           |
| BD            | float     | Bulk density of top 30 cm                            | g/cm            |
| FC            | float     | Field capacity (water holding capacity) in top 30 cm | mm              |
| WP            | float     | Wilting point for top 30 cm                          | mm              |
| PAWC          | float     | Profile available water capacity                     | mm              |

#### EMDI_ClassA_Cover_UMD_81.csv
| Variable Name | Data Type | Description                                                                       | Units              |
|---------------|-----------|-----------------------------------------------------------------------------------|--------------------|
| SITE_ID       | int       | Site identification number based on unique lat/long                               |           |
| LAT_DD        | float     | Latitude of NPP site (point)                                                      | Decimal degrees    |
| LONG_DD       | float     | Longitude of NPP site (point)                                                     | Decimal degrees    |
| WATER         | na        | Water                                                                             | 0                  |
| NEEDLE_E      | na        | Evergreen needleleaf forests                                                      | 1                  |
| BROAD_E       | na        | Evergreen broadleaf forests                                                       | 2                  |
| NEEDLE_D      | na        | Deciduous needleleaf forests                                                      | 3                  |
| BROAD_D       | na        | Deciduous broadleaf forests                                                       | 4                  |
| MIXED         | na        | Mixed forests                                                                     | 5                  |
| WOODLAND      | na        | Woodlands                                                                         | 6                  |
| WOODGRSS      | na        | Wooded grasslands/ shrubs                                                         | 7                  |
| SHRUB_CL      | na        | Closed bushlands or shrublands                                                    | 8                  |
| SHRUB_OP      | na        | Open shrublands                                                                   | 9                  |
| GRASS         | na        | Grasses                                                                           | 10                 |
| CROP          | na        | Croplands                                                                         | 11                 |
| BARE          | na        | Bare ground                                                                       | 12                 |
| URBAN         | na        | Urban and built-up                                                                | 13                 |
| COVR1KM       | int       | Dominant UMD land cover type based on a 1 km grid cell centered on the site       | Encoded from above |
| COVR50KM      | int       | Dominant UMD land cover type based on 0.5-degree  grid cell centered on the site. | Encoded from above |

#### Limitations
The authors extensively discuss their sources, limitations, and methodologies in their [data description](https://daac.ornl.gov/NPP/guides/NPP_EMDI.html). Additionally, they separate their results into three different classes based on those data sources and limitations: A, B, and C. I will only be using the highest quality predictions their modeled provided in their ClassA datasets.

#### License - IS THIS OKAY?
> To acknowledge the scientists who have created and shared data products, you should include a bibliographic citation to all data products that you use in your publications. Proper citations, including the authors, title, publisher, and DOI, will help others find and re-use the data.

* Olson, R.J., J.M.O. Scurlock, S.D. Prince, D.L. Zheng, and K.R. Johnson (eds.). 2013. NPP Multi-Biome: NPP and Driver Data for Ecosystem Model-Data Intercomparison, R2. Data set. Available on-line [http://daac.ornl.gov] from Oak Ridge National Laboratory Distributed Active Archive Center, Oak Ridge, Tennessee, USA. doi:10.3334/ORNLDAAC/615

## Research Questions
1. Which indicators are the most important for predicting the cause of a fire?
2. Can I generate a reliable model to assist investigators in determing the cause of a fire?

## Tools

### Software and Hardware
The major application to process and clean the data will be Python and Jupyter Notebook with the Pandas library. The size of the dataset should fit in memory on my main desktop PC for cleaning and preprocessing (~32gb). But, I will switch to using an Amazon Web Services (AWS) EC2 instance if necessary. Computing distance metrics between weather/bio stations and fire locations will require massive parallel processing as the algorithm will be require a pruned O(n^2) time complexity. These tasks will be performed using a compute optimized EC2 instance with as many cores as I need. Modeling the data will require significantly more space. For this, I plan on using a Spark EMR cluster on AWS.

### Algorithms
Prior to any algorithms I plan on splitting the dataset into 2 parts. The first part will contain ~80% of the data and will be used for 10-Fold cross validation and parameter selection. The final 20% will be used to determine model generalizability and final accuracy. The feature space will be considerably large when the final combined data set is finished. As such, I will employ various dimensionality reduction techniques - Principle Component Analysis (PCA) and/or Least Absolute Shrinkage and Selection Operation (LASSO) to identify the features which provide the most predictive power. The primary goal is to predict the cause of a fire. This is a multiclass classification problem with 13 different predictions. The output space is not too large so my first attempt will be to take an all-pairs method with Logistic Regression and Gradient Boosting. If time does not permit I will fail over to One-vs-Rest using the same learners. And finally, I'd like to perform a simply Ridge Regression on area covered and/or fire duration to see which independent variables are most important in assessing risk factors.

## References
* Short, Karen C. 2017. Spatial wildfire occurrence data for the United States, 1992-2015 [FPA_FOD_20170508]. 4th Edition. Fort Collins, CO: Forest Service Research Data Archive. https://doi.org/10.2737/RDS-2013-0009.4

* Gillett, N. P., & Weaver, A. J. (2004). Detecting the effect of climate change on Canadian forest fires. AGU100 Advancing Earth and Space Science, 31(18). Retrieved from https://agupubs.onlinelibrary.wiley.com/doi/full/10.1029/2004GL020876

* Forests and climate change. (2017, November 10). Retrieved November 21, 2018, from https://www.iucn.org/resources/issues-briefs/forests-and-climate-change

* The Paris Agreement | UNFCCC. (n.d.). Retrieved November 21, 2018, from https://unfccc.int/process-and-meetings/the-paris-agreement/the-paris-agreement

* Forests provide a critical short-term solution to climate change. (2018, June 22). Retrieved November 21, 2018, from http://www.unenvironment.org/news-and-stories/story/forests-provide-critical-short-term-solution-climate-change

* Facts + Statistics: Wildfires | III. (n.d.). Retrieved November 21, 2018, from https://www.iii.org/fact-statistic/facts-statistics-wildfires

* NPP Multi-Biome: NPP and Driver Data for Ecosystem Model-data Intercomparison, R2. (n.d.). Retrieved November 21, 2018, from https://daac.ornl.gov/NPP/guides/NPP_EMDI.html

* Olson, R.J., J.M.O. Scurlock, S.D. Prince, D.L. Zheng, and K.R. Johnson (eds.). 2013. NPP Multi-Biome: NPP and Driver Data for Ecosystem Model-Data Intercomparison, R2. Data set. Available on-line [http://daac.ornl.gov] from Oak Ridge National Laboratory Distributed Active Archive Center, Oak Ridge, Tennessee, USA. doi:10.3334/ORNLDAAC/615
