# Predicting the Source of Wildfires and Further Analysis
Luke Waninger  
DATA 512 Final Project  
University of Washington, Fall 2018  
__[The Live Notebook](https://notebooks.azure.com/lukewaninger/projects/wildfires)__

## Abstract
Wildfires have been a big topic in the recent news with devasting effects across the western coast of the United States. So far this year, we have had less burn than 2017, but the current fire in California is the largest in state history and still burns rapidly. Last year, we had almost 2 billion dollars of losses across the United States as a result of wildfire damage which has been the highest in history [[6](https://www.iii.org/fact-statistic/facts-statistics-wildfires)]. Risks of wildfires continue to climb as scientists discover alarming links between rising greenhouse gasses, temperature, and wildfire severity. N. P. Gillett et al. performed a comprehensive study on the relationship between the two and concluded with overwhelming confidence that a positive trend exists between them [[2](https://agupubs.onlinelibrary.wiley.com/doi/full/10.1029/2004GL020876)]. Rising greenhouse gasses could be playing a significant role in the prevalence and severity of forest fires. The visualization below shows a scatter plot of wildfires recorded in the continental US from 1991-2015. Each point is a fire (I'll go into the colors later in the notebook). This picture shows the the magnitude and prevalance of the problem here at home and gives me further creedance to study the problem.

![Map of Continental US Wildfires](https://github.com/lukeWaninger/hcds-final/blob/master/images/all_fires_map.JPG "Map of Continental US Wildfires")

Key to understanding the overall problem is the double-edged sword forests play in climate change; they are both a cause and effect. The wildfires both increase atmospheric greenhouse gasses and destroy the integral vegetation to the planet's carbon cycle [[3](https://www.iucn.org/resources/issues-briefs/forests-and-climate-change), [7](https://daac.ornl.gov/NPP/guides/NPP_EMDI.html), [8](http://daac.ornl.gov/)]. The Paris Agreement has specifically mentioned the importance of this and insists that countries protect against deforestation [[4](https://unfccc.int/process-and-meetings/the-paris-agreement/the-paris-agreement)]. Not only is the world pushing to keep the forests we have but here at home, we have begun to employ them as significant combatants in the fight against climate change. California has led the way with their proposed carbon plan. It proposes methods to reshape parts of their existing ecosystem to make their forests even more efficient at removing carbon [[5](http://www.unenvironment.org/news-and-stories/story/forests-provide-critical-short-term-solution-climate-change)]. Stopping deforestation would significantly promote the UNs progress towards reaching goals outlined in the Paris Agreement.

However, this will not work if the forests continue in the same destructive cycle with our ecosystem. The goal of this project is two-fold. One, to understand the independent variables and correlation effects in a combined dataset of the Fire Program Analysis (FPA) reporting system, NOAA's Global Surface Summary of Day Data (GSOD) 7, and  NASA's biomass indicators. Two, to train and assess a model for predicting the reason a wildfire started. (and possibly estimate the impact? location?) Identifying the source is a difficult task for investigators in the wild. The vastness of land covered is much larger than the matchstick or location of a lightning strike. Developing an understanding of the independent variables and a reliable prediction model could give authorities valuable direction as to where to begin their search.

#### Research Questions
* What are the most important indicators to consider when determining the cause of a wildfire?
* Can a reliable model be built to assist investigators in determining the cause of a wildfire?

####  Reproducibility
This notebook is intended to be completely reproducible. However, the starting datasets are much too large to be hosted on GitHub. I provide a small, randomly selected sample with the repository to show the dataset cleaning and generation process. The full notebook and data can be ran, cloned, or downloaded through [Azure Notebooks](https://notebooks.azure.com/lukewaninger/projects/wildfires). If you run this notebook on your own machine please be aware that the notebook requires quite a bit of resources. With 12 cores running at 4ghz and a consistent 95% CPU load, it took my machine nearly 27 hours to compute. The analysis portion of the notebook is also computationally expensive. The cross-validation approach implemented will consume all available resources and severely limit any other concurrent processes for several hours. The final tuned models can be computed directly via the parameters found during my tuning process.

The original data format of the GSOD data makes creating samples a bit challenging. To do this, I ran an additional notebook with the following code. It opens each subdir of the extracted GSOD file and randomly selects and removes half the files. I ran this iteratively until the resulting file size was within the Github file size limit of 100mb.

<details><summary>expand</summary>  

```Python
import os

# walk the extracted directory
for dirpath, dirnames, filenames in os.walk('gsod_all_years'):
    
    # process each year
    for sdir in dirnames:
        # randomly select some station files        
        sfiles = os.listdir(os.path.join(dirpath, sdir))
        to_remove = np.random.choice(sfiles, int(len(sfiles)/2))
        
        # remove them
        for f in to_remove:
            try:
                tr = os.path.join('.', dirpath, sdir, f)
                os.remove(tr)
            except FileNotFoundError:
                pass
```    
</details>  
  
I repacked the sample to be in the same format as the original dataset using WinZip. To sample from the completed fires dataset I used the following code snippet.

<details><summary>expand</summary>  
  
```Python
import pandas as pd

# read
df = pd.read_csv('fires_complete.csv')

# sample
df = df.sample(frac=.85)

# write back
df.to_csv('fires_complete.csv', index=None)
```
</details>  
  
And finally, to sample the fires data I first dropped all other tables besides Fires. Next, I ran the following snippet iteratively until the sqlite file was under 100mb.  
  
<details><summary>expand</summary>  

```Python
import sqlite3

# connect
path = os.path.join('FPA_FOD_20170508.sqlite')
conn = sqlite3.connect(path)

# randomly delete some fires
conn.execute("""
  DELETE FROM Fires
  WHERE fod_id IN (
    SELECT fod_id
    FROM Fires 
    ORDER BY RANDOM() 
    LIMIT 100000
  );
""")

# compress the file
conn.execute('VACUUM;')
```  
</details>

#### A Note on Visualizations
I use [Plotly](https://plot.ly/python/) extensively throughout this notebook. They are interactive and require Javascript to be running in the background. The Github previewer does not run the necessary Javascript for rendering making them just empty grey squares.

## Limitations
An important limitation to mention is the nature of the wildfires dataset. It was aggregated over 25 years of varying federal and local agencies. This becomes evident when taking a look at the scatter plot shown above. Kentucky seemed to place heavy importance on reporting campfire caused incidents; noticeable by the distinct outline around the state. Other states of interest are Louisiana and New York. The majority of Louisiana fires had missing or undefined classification labels. New York stands out for for the extreme level of reporting. In stark contrast, Virginia is clearly depicted against the dense reporting of bordering states.

An additional limitation is the bias in weather station location placement. Roughly 25% of wildfires occurred more than 55km from the nearest station. The resulting join created a segmented pattern of missing geographic regions. In turn, the model suffers from a bias where those empty regions may have less predictive power than those with stations nearby.

![Spatial Bias](https://github.com/lukeWaninger/hcds-final/blob/master/images/spatial_bias.JPG "Spatial Bias")

This is an important implication for local authorities that may lie within the sparse regions shown above. 

## Conclusions
####  What are the most important indicators to consider when determining the cause of a wildfire?
Some interesting features turned out to be the most important for determining the cause of a fire.  

![Feature Importance](https://github.com/lukeWaninger/hcds-final/blob/master/images/Rank%20of%20Feature%20Importance.png)
I expected the month of the year to have an impact on the occurences of wildfires but not necessarily the cause. Arsonists and children, for example, have a clear season of burning it seems.

_[See the interactive plot](https://plot.ly/~waninger/30/)_  
![The Seasonality of Wildfires by Cause](https://github.com/lukeWaninger/hcds-final/blob/master/images/The%20Seasonality%20of%20Wildfire%20Causes.png)

As it turns out, weather doesn't correlate very well to the cause of a fire. It happens that more lightning occurs with both drier climates and tends to start fires more easily. None of that should surprise anyone. 

Longitude had a surprisingly high impact as well. This is evident from the US map above. Again, arsonists are standing out in the data. We can see they tend to burn more heavily in the South Eastern states and tend to shy away from federally owned lands.

I expected to learn a great deal from joining the vegetation and soil content data. I'm disappointed that we were unable to take advantage of the information. In the future, I plan on using the Google Earth Engine for any environmental related products that I produce. The engine demanded too steep of a learning curve for me to utilize in this project but I look forward to learning it. Despite the setback we still gathered some useful information. 

#### Can a reliable model be built to assist investigators in determining the cause of a wildfire?
With the features we have right now I wouldn't say that our multi-class classification model was very reliable for predicting one of the 13 reasons. Predicting arson caused fires was decently successful with 87% area under the curve (AUC) for cross-validation.

![ROC](https://github.com/lukeWaninger/hcds-final/blob/master/images/ROC.png)

I have no doubt that we can obtain better performance with added vegetation data. There many areas of the data that have yet to be explored.

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