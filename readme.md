# Predicting the Source of Wildfires and Further Analysis
Luke Waninger

DATA 512 Final Project

University of Washington, Fall 2018

## Introduction
Wildfires have been a big topic in the recent news with devasting effects across the western coast of the United States. So far this year, we have had less burn than 2017, but the current fire in California is the largest in state history and still burns rapidly. Last year, we had almost 2 billion dollars of losses across the United States as a result of wildfire damage which has been the highest in history [[6](https://www.iii.org/fact-statistic/facts-statistics-wildfires)]. Risks of wildfires continue to climb as scientists discover alarming links between rising greenhouse gasses, temperature, and wildfire severity. N. P. Gillett et al. performed a comprehensive study on the relationship between the two and concluded with overwhelming confidence that a positive trend exists between them [[2](https://agupubs.onlinelibrary.wiley.com/doi/full/10.1029/2004GL020876)]. Rising greenhouse gasses could be playing a significant role in the prevalence and severity of forest fires. The visualization below shows a scatter plot of wildfires recorded in the continental US from 1991-2015. Each point is a fire (I'll go into the colors later in the notebook). This picture shows the the magnitude and prevalance of the problem here at home and gives me further creedance to study the problem.

![US Continental Map of Wildfires](https://github.com/lukeWaninger/hcds-final/blob/master/images/all_fires_map.JPG "[US Continental Map of Wildfires")

Key to understanding the overall problem is the double-edged sword forests play in climate change; they are both a cause and effect. The wildfires both increase atmospheric greenhouse gasses and destroy the integral vegetation to the planet's carbon cycle [[3](https://www.iucn.org/resources/issues-briefs/forests-and-climate-change), [7](https://daac.ornl.gov/NPP/guides/NPP_EMDI.html), [8](http://daac.ornl.gov/)]. The Paris Agreement has specifically mentioned the importance of this and insists that countries protect against deforestation [[4](https://unfccc.int/process-and-meetings/the-paris-agreement/the-paris-agreement)]. Not only is the world pushing to keep the forests we have but here at home, we have begun to employ them as significant combatants in the fight against climate change. California has led the way with their proposed carbon plan. It proposes methods to reshape parts of their existing ecosystem to make their forests even more efficient at removing carbon [[5](http://www.unenvironment.org/news-and-stories/story/forests-provide-critical-short-term-solution-climate-change)]. Stopping deforestation would significantly promote the UNs progress towards reaching goals outlined in the Paris Agreement.

However, this will not work if the forests continue in the same destructive cycle with our ecosystem. The goal of this project is two-fold. One, to understand the independent variables and correlation effects in a combined dataset of the Fire Program Analysis (FPA) reporting system, NOAA's Global Surface Summary of Day Data (GSOD) 7, and  NASA's biomass indicators. Two, to train and assess a model for predicting the reason a wildfire started. (and possibly estimate the impact? location?) Identifying the source is a difficult task for investigators in the wild. The vastness of land covered is much larger than the matchstick or location of a lightning strike. Developing an understanding of the independent variables and a reliable prediction model could give authorities valuable direction as to where to begin their search.

#### Research Questions
* What are the most important indicators to consider when determining the cause of a wildfire?
* Can a reliable model be built to assist investigators in determining the cause of a wildfire?

####  Reproducibility
This notebook is intended to be completely reproducible. However, the starting datasets are much too large to be hosted on GitHub. I provide a small, randomly selected sample with the repository to show the dataset cleaning and generation process. The full datasets can be downloaded by running the following cell. Running it will overwrite the sample provided. I highly advise not attempting to run the full dataset. Generating the weather aggregations across the entire fires dataset can take a considerable amount of time. With 12 cores running at 4ghz and a consistent 95% CPU load, it took my machine nearly 27 hours to compute. The analysis portion of the notebook is also computationally expensive. The cross-validation approach implemented will consume all available resources and severely limit any other concurrent processes for several hours. The final tuned models can be computed directly via the parameters found during my tuning process.

It is also possible to view the live notebook on [here](#blank).

#### A Note on Visualizations
I use [Plotly](https://plot.ly/python/) extensively throughout this notebook. The majority of the visualizations are interactive and require Javascript to be running in the background. The Github previewer does not run the necessary Javascript for rendering making them just empty grey squares. Due to this restriction, I've published the notebook to Azure Notebooks where you can view and or rerun as necessary. You may also clone or download the notebook directly from Azure. Cloning from there gives the added benefit of not needing to download the data in the following cell. But running in Azure poses additional limitations for Plotly. The notebook needs to be running in Trusted mode for the Javascript to execute as needed [11](https://media.readthedocs.org/pdf/jupyter-notebook/stable/jupyter-notebook.pdf).

## Limitations
An important limitation to mention is the nature of the wildfires dataset. It was aggregated over 25 years of varying federal and local agencies. This becomes evident when taking a look at the map at the beginning of the notebook. Kentucky seemed to place heavy importance on reporting campfire caused incidents. You can see this by the distinct outline of a unique color around the state. Other states of interest are Louisiana and New York. The majority of Louisiana fires had missing or underfined classification labels. New York stands out for for the extreme level of reporting. It's very easy to pick the state of the scatter plot even though no boundaries were drawn. In contrast, the Southern border of Virginia is starkly depicted against the dense reporting of North Carolina.

An additional limitation is the bias in weather station location placement. Roughly 25% of wildfires occurred more than 55km from the nearest station. This may not cause a problem with our dataset given how insignificant the majority of our weather columns were in contributing to model inference. But, it is something that should be noted for future work.

## References
[1] Short, Karen C. 2017. Spatial wildfire occurrence data for the United States, 1992-2015 [FPA_FOD_20170508]. 4th Edition. Fort Collins, CO: Forest Service Research Data Archive. https://doi.org/10.2737/RDS-2013-0009.4

[2] Gillett, N. P., & Weaver, A. J. (2004).Detecting the effect of climate change on Canadian forest fires. AGU100 Advancing Earth and Space Science, 31(18). Retrieved from https://agupubs.onlinelibrary.wiley.com/doi/full/10.1029/2004GL020876

[3] Forests and climate change. (2017, November 10). Retrieved November 21, 2018, from https://www.iucn.org/resources/issues-briefs/forests-and-climate-change

[4] The Paris Agreement | UNFCCC. (n.d.). Retrieved November 21, 2018, from https://unfccc.int/process-and-meetings/the-paris-agreement/the-paris-agreement

[5] Forests provide a critical short-term solution to climate change. (2018, June 22). Retrieved November 21, 2018, from http://www.unenvironment.org/news-and-stories/story/forests-provide-critical-short-term-solution-climate-change

[6] Facts + Statistics: Wildfires | III. (n.d.). Retrieved November 21, 2018, from https://www.iii.org/fact-statistic/facts-statistics-wildfires

[7] NPP Multi-Biome: NPP and Driver Data for Ecosystem Model-data Intercomparison, R2. (n.d.). Retrieved November 21, 2018, from https://daac.ornl.gov/NPP/guides/NPP_EMDI.html

[8] Olson, R.J., J.M.O. Scurlock, S.D. Prince, D.L. Zheng, and K.R. Johnson (eds.). 2013. NPP Multi-Biome: NPP and Driver Data for Ecosystem Model-Data Intercomparison, R2. Data set. Available on-line http://daac.ornl.gov from Oak Ridge National Laboratory Distributed Active Archive Center, Oak Ridge, Tennessee, USA. doi:10.3334/ORNLDAAC/615

[9] 2010-01-30: Surface Summary of Day, GSOD - Datafedwiki. https://data.nodc.noaa.gov/cgi-bin/iso?id=gov.noaa.ncdc:C00516

[10] About Us - ORNL DAAC. https://daac.ornl.gov/about/

[11] Jupyter Notebook Documentation. Release 5.7.2, Section 1.8 Trusting Notebooks. https://media.readthedocs.org/pdf/jupyter-notebook/stable/jupyter-notebook.pdf