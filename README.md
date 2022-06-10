
# PPHA 30536 (Autumn 2021) Data & Programming for Public Policy II Final Project

## Introduction:

The provision of broadband internet is imperative in today's society, and a significant body of research shows that broadband is important for rural economic growth. The importance of broadband access is becoming increasingly important in farm communities to enable increased income generation through methods like precision agriculture. Several different policies exist at the federal and state level which impact how quickly and easily broadband is diffused into rural areas. States may have specific funds, agencies, taskforces, or restrictions which bring a wide variance in outcomes for rural counties. Municipal broadband restrictions prevent local governments from creating their own solutions in counties where private providers are not meeting needs of its residents, which tend to be rural communities.

## Research Question:

My analysis will look at how state-level policy, specifically municipal broadband restrictions, affects broadband outcomes. I will look at the impacts on the number of internet providers, broadband access, and average cost of lowest priced plans for farm-dependent counties. I added additional controls for demographic characteristics that might also affect broadband availability, such as poverty rate and amount of housing units. Additionally, I added a control for USDA Rural Development federal funding as this varied greatly by state and directly impacts broadband availability in rural, farm counties.

## Data and Methods:

There is criticism on the accuracy of the FCC data, which is used in most maps and studies around broadband, so I wanted to include both Microsoft and FCC data to visualize the differences side-by-side:
- Internet provider plan pricing data at the zip code level from BroadbandNow (https://github.com/BroadbandNow/Open-Data)
- Broadband access and usage percentage data at the county level from Microsoft Open Source Initiative (https://github.com/microsoft/USBroadbandUsagePercentages)

- County level indicator data from the USDA Economic Research Service (ERS) for rural counties (no cities with 50,000 residents or more) and farm-dependent counties (faming accounted for at 25% or more of the county's earnings or 16% or more of the employment averaged over 2010-2012) (https://www.ers.usda.gov/data-products/county-typology-codes)
- Webscraping to retrieve data from the National Association of Counties (NACO) to get county-level data on internet providers, poverty rate, housing, and USDA Rural Development grants and loans (https://ce.naco.org/)
- The policy indicator was created using the list of states that have municipal broadband restrictions according to Pew Charitable Trusts state broadband policy explorer (https://www.pewtrusts.org/en/research-and-analysis/data-visualizations/2019/state-broadband-policy-explorer)

I provide summary tables which summarize data at the county and state level as well as by farm and policy indicator.

## Obstacles:

I ran into a few challenges with merging the different datasets together as the BroadbandNow data was at the zip code level, while the other 3 sources were at the county level. This posed an issue in both aggregating the zip codes accurately to the county level and merging as there was no FIPS code to join on in this dataset. Some zip codes exist in multiple counties and across state lines, which added complexity to the aggregation to the county level. I chose to ignore this additional layer as I didn't have zip code level data for all variables used and could not be sure if the other data was also aggregated in this same way. I made the assumption that county data was only included for state where majority of population was. Many of the variables I had planned to use from this dataset were no longer viable as there was no way to know if there is overlap in the values across zip codes within a count. For example, with provider count there was the risk of overcounting by simply summing all the to the zip codes within a county or undercounting by taking the mean. Due to the uncertainty around the accuracy of the aggregation, I decided to only use the mean lowest-priced broadband plan price from the BroadbandNow dataset in my analysis.

To merge the BroadbandNow data with the rest of the sources, I had to join on the county name. This posed an additional set of challenges in getting the columns to match with differences in character case, special characters, spacing, and other naming nuances. I created a few functions to clean this as best as possible, accepting the fact that there were some counties in each dataset that simply did not exist in the others. This likely based on the methodology used and year of collection. The NACO data also excluded some counties in AK, CT, MA, NY, VA, and RI because they had no county governments, but I kept these counties in as some of the other variables were still available and were still able to be displayed in the maps. 

## Results:

### OLS Model 1:

Providers= β_0+ β_1 Metro_ind + β_2 Farm_ind + β_3 Policy_ind + β_4 USDA_Amount+ β_5 Poverty + β_6 Housing+ ε

Based on the results, we see that farm-dependent counties with municipal broadband restrictions are associated with ~1.58 fewer providers than non-farm counties. All of the results were significant at the 1% level. This implies that there is potentially a statistically significant impact between farm counties, municipal broadband restrictions, and the number of providers.

### OLS Model 2:

Availability= β_0+ β_1 Rural + β_2 Farm + β_3 Policy + β_4 USDA_Amount+ β_5 Poverty + β_6 Housing+ ε

Based on the results, we see that farm-dependent counties with municipal broadband restrictions are associated with ~0.038 lower rate of broadband availability than non-farm counties. However, in this case the farm indicator was not significant even at the 5% level, suggesting that whether a county is farm-dependent does not have a statistically significant impact on broadband availability.

### OLS Model 3:

Mean_plan_price= β_0+ β_1 Rural + β_2 Farm + β_3 Policy + β_4 USDA_Amount+ β_5 Poverty + β_6 Housing+ ε

Based on the results, we see that farm-dependent counties with municipal broadband restrictions are associated with lowest priced plans that are $4.61 more on average compared than non-farm counties. In this case, the policy indicator was not significant even at the 5% level, suggesting that municipal broadband restriction policy doesn’t have a statistically significant impact on plan pricing.

## Limitations:

OLS is not the best model to estimate this result as there is an endogeneity problem with the right-hand side variables, especially the policy measure, and the independent variables. There is also no element of time included in the model, and this could have an impact on the results depending on when the policy was enacted and how results have changed over time. A more detailed model that includes fixed effects of instrumental variables could potentially solve for this. I have seen literature that answers a similar research question that uses more complex models to handle the analysis and while the results trend in the same direction, due to the bias introduced from these methods, the results should be taken with a level of caution.

A more accurate measure on the direct impacts to farms would have included variables that showed the direct benefit to farms in terms of increased crop yield or cost savings from broadband access. Although I found some literature, I was not able to locate enough and recent enough data around this from the USDA to include this in the model. It also would have been more accurate to include data on all the state-level policies as these likely have a compounding impact on the broadband outcomes, however I had trouble finding this data compiled in a usable format, so I opted to create my own variable for just one of the policies.
