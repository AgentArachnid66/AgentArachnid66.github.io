+++
title = 'Data Engineering'
date = 2023-09-05T11:46:29+01:00
+++


## Storyplaces

I was employed as a placement with my University. I was given a goal to present my analysis findings to a group of PhD students, and to help me with this my manager gave me a list of tasks that needed doing from the weekly level, with an individual list of goals for each week. I used primarily Python and more specifically Pandas for this project. 

For the first week it was purely exploratory analysis, finding basic things out like how many users on any given day, how long did each user spend on average, etc. 

The second week was focused on more in depth analysis, more specifically user classification. Firstly, I had to come up with an algorithm that would take in an individual's metrics, calculated by crossreferencing the analysis I did in the first week, and designate a classification.

The third week was centred around choice analysis and pattern recognition, like if 2 pages were far apart if that effected player's choice or if a certain path is popular and why? I created a graphical representation of the story, and using this I created a list of every possible path in the story in varying lengths between the distance between 2 nodes and from the top and bottom of the graph. Then converting these paths to a string that could be looked up in each player's path, I could easily get the frequency of each path.

The final week was preparing for the presentation, so I was creating visualisations for the data. I used R Studio & Excel to create the geographical visualisations and Python for the other graphs. 


## Conference Paper

I was recommended to do some data engineering for another professor at Bournemouth University whilst I was in my final semester and the **[paper](https://www.researchgate.net/publication/376789395_Association_between_air_temperature_and_unintentional_drowning_risk_in_the_United_Kingdom_2012-2019_A_nationwide_case_crossover_study)** was published. I was given a list of data files and was told that I had to find the coordinates of each incident in the file and the closest station associated with the station. This would essentially find which station is responsible for each drowning incident. The simplest way of determining the distance was using the Haversine Formula. This alone provided some interesting challenges as the data showed which stations were permanently closed down before the incident and some were still open but the incident happened outside of their normal operational hours, so my method getting around this was if one station was invalid, then it would recursively search for the next nearest one. It would do this until either a valid station is found or the search limit is reached, I set this to 10 by default. My final task was to add the Air Temperature and Rainfall metrics from the previous 14 days from the incident date to the data set and then export it.