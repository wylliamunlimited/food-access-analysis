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
1. Filter `low access to store` population by isolating based on a threshold - any data with population smaller than the threshold is removed
   * This ensures the foundational potential reach of the food access program
2. Sort the filtered population by combined score
   * This indicates the counties with not only a group of audience for food access program - they also present higher needs, influenced by factors beyond locational limitation
3. Select the top 5 ranked counties

### Visualization
1. Plot top 10 crude prevalence for the 5 counties - bar plot
2. Plot the 5 counties by population of low access to store - bar plot
3. Plot top 5 combined score, with access score & disability score on the side