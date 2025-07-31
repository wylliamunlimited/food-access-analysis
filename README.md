# Food Access Analysis

## Goal
* Break down demands for food access & trends for food insecurity
* Suggest final specifications of a food access program plan
* Analyze projected impacts & predicted achievements

## Dataset(s)
* [CDC 500 Cities Project](https://chronicdata.cdc.gov/500-Cities/500-Cities-Census-Tract-level-Data-GIS-Friendly-Fo/k86t-wghb)
* [FDA - Food Atlas](https://www.ers.usda.gov/data-products/food-environment-atlas/data-access-and-documentation-downloads/)

## Installations
```pip install -r requirements.txt```

## Using the Data
* Unzip the `challenge.db.zip`
* Use `sqlite3` & SQL queries to access data within `challenge.db` - check out beginning of `data-exploration.ipynb`

## Steps

### Preprocessing
1. Aggregate CDC 500 Cities data by county
The CDC 500 Cities dataset is highly granular, reporting at the census tract or city level. However, food access programs are typically deployed at the county or multi-county level for scalability, operational feasibility, and alignment with existing public health infrastructures. Therefore, aggregating to the county level provides a more practical unit for prioritization and resource allocation.
2. Select columns for analysis
To guide prioritization, I grouped features into two categories:
    * Foundational population metrics: These represent the potential scale or reach of a program (e.g., total low-access population). They help answer the question: “How many people might benefit from this program?”
    * Vulnerability metrics: These describe the depth of need within that population — such as income level, transportation access, disability, and chronic health conditions. These help assess: “How severely is this county affected by food insecurity?”
This separates the analysis over the size of affected population and the structural barrier to healthy food access.
3. Custom metric creation - score system
We compute the following metrics for a better quantified analysis:
    * Access score: computed by multiplying normalized food access data + weight (to be assigned based on other assumptions, e.g. low income drives food insecurity more rapidly than no car does, etc.)
    * Disability score: computed by multiplying normalized disease + weight (to be assigned based on other assumptions, e.g. diabetes has stronger connection than arthritis does to food/nutrition health access, etc.)
    * Combined score: computed from access & disability scores
P.S. Rate of change data (e.g. 2010-2015 change of population with low access to store) will be used as penalty terms to include the change behavior across time.

### Analysis
1. Sort the filtered population by combined score
   * This indicates the counties with not only a group of audience for food access program - they also present higher needs, influenced by factors beyond locational limitation
   * Observe the amount of population - check if their size is above 100000 to guarantee foundational reach 
3. Select the top 10 ranked counties - plot them

### Notes
1. The prevalence metrics are estimated from either 2016 or 2017 while the population was from 2010 census - mismatch 
2. Estimation of impacted population are from the rate of change between 2010 to 2015 and compounded with 2015 population as a basis
    * The rate of change is only 2 data point 2010 and 2015 so it is a very rough estimate, so the estimates are being rounded up to account for potential growths

### Deployment Details
* The program should be launched in Texas, specifically Harris County.
    * The most populated with low access to store community
    * Access data → highest food insecurity risk
    * Disability data → highest need for healthy food access 
* At launch, the program can adjust food offerings by creating plan options specific to obesity, high cholesterol & high blood pressure individuals 
Data indicates they occupy over ¼ of the population within the county - significant for program fine tuning
* State wise - start from TX, given state-to-state policies
    * Scalability is also among the top 3 states 
        * High concentration of counties with many individuals who have low access to store
    * Total population with low access to store is the highest among all state

### Key Data Insights
* General analysis specs
    * Took into account of all variables
    * Weighted scoring system - variables that are more connected to the demand of food access programs are emphasized, e.g. low income population
    * Penalty analysis - the score is refined by the rate of change of population as well, so that we also consider the population size the program will serve, e.g. a decrease in the low income population size from 2010 to 2015 will lower the current need of the food program
* Disability trend analysis
    * Most disability prevalence range from 5% to 20% with 3 exceptions - Obesity, high cholesterol, high blood pressure - closely connected to food nutrition 
* Access trend analysis
    * Harris, TX has the highest population of children with low access to stores
    * Highest population of low income & low access, and SNAP household counts
    * Among the top 5 counties populated with no car


### Visualization
1. Plot top 10 combined scores for the counties - bar plot
2. Plot the 10 counties by population of low access to store - bar plot
3. Plot pie charts for demographic illustrations
4. Plot map plot for low access to store population
5. Plot heatmap for distribution of low access population county counts - cut by threshold of 100000

### Q & A
#### Why racial profile pie chart confident? 
Census data only contains those racial groups and access dataset is based on 2010 census data
#### Why analyze based on population but not percentage?
* Program deployment should prioritize reach in the beginning stage - population indicates potential reach
* Percentage could only tell us the portion of specific demographics or environment of the county
#### Why choose a scoring system?
This way we put all impacting variables into account with priorities over certain factors that influence the demand for food access more
#### How are the weights determined?
* Access:
    * Variables that directly impact access (transport) is prioritized
    * Variables that are confounding variables or non-immediate trigger of food access demand are de-prioritized
    * primary impact \[0.6\]: low income, no car
    * secondary impact \[0.4\]: SNAP, senior, children
* Disability:
    * High: Prevalence of variables that directly relate to nutrition (diabetes, obesity, high cholesterol, etc.)
    * Medium: Prevalence of variables that are other kinds of conditions - less related to food (arthritis, etc.)
    * Low: Prevalence of variables that describe behaviors, medical access, etc. (population with insurance, etc.)
    * \[nutrition related disease\] high (0.6): obesity, diabetes, highchol, bphigh
    * \[health conditions\] medium (0.3): phlth, mhlth, kidney, copd, chd, asthma, cancer, arthritis
    * \[behavior / daily life\] low (0.1): teethlost, sleep, paptest, mammouse, lpa, dental, csmoking, corew, corem, colon_screen, cholscreen, checkup, bpmed, binge, access2
#### Why isolate the scores into access & disability?
Access directly presents the need while disability presents the specification and therefore a need indirectly
#### Why aggregate the cities data into county based?
* Business wise - looking at tract/city is too specific for a program to operate, might lead to over-customization over that tract and minimize scalability
* Data Analysis - It presents a clear comparison between counties & cross support the access data
#### Why include disability data into decision making?
Disability determines the specification of the programs, etc. 
