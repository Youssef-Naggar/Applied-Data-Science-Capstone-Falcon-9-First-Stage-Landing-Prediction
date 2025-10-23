# Applied Data Science Capstone: Falcon 9 First Stage Landing Prediction

## Overview

Welcome to the Applied Data Science Capstone Project. This repository documents the complete data science workflow used to predict the landing success of the Falcon 9 rocket's first stage.

SpaceX advertises Falcon 9 launches at a competitive cost of \$62 million, significantly lower than other providers who charge upwards of \$165 million. This cost efficiency is largely achieved by successfully landing and reusing the first stage. By building a predictive model for landing success, we can more accurately estimate launch costs. This analysis provides valuable intelligence for companies and stakeholders competing for or bidding on space-related launch contracts.

This project was completed as part of the **IBM Data Science Professional Certificate** and utilizes a range of data science techniques, from data collection and web scraping to machine learning and interactive visualization.

## Project Workflow and Results

The project was structured into a series of modules, each building upon the last. Below is a detailed breakdown of the methodology and the key results achieved in each stage.

---

### 1\. Data Collection and Wrangling

**Methodology:**

- **API Data Collection:** Historical launch data was gathered by making a GET request to the SpaceX REST API.
    
- **Web Scraping:** Falcon 9 launch records were extracted from a Wikipedia table using Python's `BeautifulSoup` library.
    
- **Data Cleaning:** The collected data from both sources was integrated and then cleaned. This involved handling missing values, standardizing formats, and preparing the dataset for analysis and feature engineering.
    

---

### 2\. Exploratory Data Analysis (EDA)

**Methodology:** A comprehensive EDA was performed to uncover initial patterns and correlations. This involved executing SQL queries on a Db2 database and generating a series of visualizations using `Matplotlib` and `Seaborn`.

#### Key Insights (Visualization)

- **Flight Number vs. Launch Site:** The CCAFS SLC 40 and KSC LC 39A sites show a mix of successful (Class 1) and unsuccessful (Class 0) landings. This suggests that while the launch site is a factor, it isn't the only determinant of success.
    
- **Payload Mass vs. Launch Site:** The KSC LC 39A site is clearly used for launching heavier payloads, with multiple launches exceeding 15,000 kg. CCAFS SLC 40 primarily handles payloads below 10,000 kg.
    
- **Success Rate by Orbit Type:** A bar chart revealed that missions to VLEO, ES-L1, GEO, HEO, and SSO orbits achieved a 100% success rate. Conversely, the GTO orbit has a significantly lower success rate, indicating greater challenges for these missions.
    
- **Annual Launch Success Rate:** A line chart of success rates by year shows a significant improvement trend starting in 2013 and reaching over 80% by 2020, indicating increasing reliability in Falcon 9 launches.
    

#### Key Insights (SQL Database Analysis)

A database was created from the cleaned dataset to derive specific insights using SQL queries.

- **Task 1: Display the names of the unique launch sites.**
    
    - **Query:** `SELECT DISTINCT "Launch_Site" FROM SPACEXTABLE;`
        
    - **Result:** `CCAFS LC-40`, `VAFB SLC-4E`, `KSC LC-39A`, `CCAFS SLC-40`
        
- **Task 2: Display 5 records where launch sites begin with the string 'CCA'.**
    
    - **Query:** `SELECT * FROM SPACEXTABLE WHERE "Launch_Site" LIKE 'CCA%' LIMIT 5;`
        
    - **Result:** Displayed the first 5 launches from `CCAFS LC-40`.
        
- **Task 3: Display the total payload mass carried by boosters launched by NASA (CRS).**
    
    - **Query:** `SELECT SUM("PAYLOAD_MASS__KG_") FROM SPACEXTABLE WHERE "Customer" = 'NASA (CRS)';`
        
    - **Result:** 45596 kg
        
- **Task 4: Display average payload mass carried by booster version F9 v1.1.**
    
    - **Query:** `SELECT AVG("PAYLOAD_MASS__KG_") FROM SPACEXTABLE WHERE "Booster_Version" = 'F9 v1.1';`
        
    - **Result:** 2928.4 kg
        
- **Task 5: List the date when the first successful landing outcome in ground pad was achieved.**
    
    - **Query:** `SELECT MIN("Date") FROM SPACEXTABLE WHERE "Landing_Outcome" = 'Success (ground pad)';`
        
    - **Result:** 2015-12-22
        
- **Task 6: List the names of the boosters which have success in drone ship and have payload mass > 4000 and < 6000.**
    
    - **Query:** `SELECT DISTINCT "Booster_Version" FROM SPACEXTABLE WHERE "Landing_Outcome" = 'Success (drone ship)' AND "PAYLOAD_MASS__KG_" > 4000 AND "PAYLOAD_MASS__KG_" < 6000;`
        
    - **Result:** `F9 FT B1022`, `F9 FT B1026`, `F9 FT B1021.2`, `F9 FT B1031.2`
        
- **Task 7: List the total number of successful and failure mission outcomes.**
    
    - **Query:** `SELECT "Mission_Outcome", COUNT(*) AS "Total" FROM SPACEXTABLE WHERE "Mission_Outcome" IN ('Success', 'Failure') GROUP BY "Mission_Outcome";`
        
    - **Result:** Success: 98
        
- **Task 8: List the names of the booster\_versions which have carried the maximum payload mass.**
    
    - **Query:** `SELECT DISTINCT "Booster_Version" FROM SPACEXTABLE WHERE "PAYLOAD_MASS__KG_" = (SELECT MAX("PAYLOAD_MASS__KG_") FROM SPACEXTABLE);`
        
    - **Result:** A list of 12 booster versions, including `F9 B5 B1048.4`, `F9 B5 B1049.4`, etc.
        
- **Task 9: List the records which will display the month names, failure landing\_outcomes in drone ship, booster versions, and launch\_site for the months in year 2015.**
    
    - **Query:** (Used `CASE` statements to extract month names) `SELECT ... WHERE substr("Date", 0, 5) = '2015';`
        
    - **Result:** A table of all 7 launches in 2015, with one 'Failure (in flight)' recorded in June.
        
- **Task 10: Rank the count of landing outcomes (such as Failure (drone ship) or Success (ground pad)) between 2010-06-04 and 2017-03-20, in descending order.**
    
    - **Query:** `SELECT "Landing_Outcome", COUNT(*) AS "Count" FROM SPACEXTABLE WHERE "Date" BETWEEN '2010-06-04' AND '2017-03-20' GROUP BY "Landing_Outcome" ORDER BY COUNT(*) DESC;`
        
    - **Result:** 'No attempt' (10), followed by 'Success (drone ship)' (5), 'Failure (drone ship)' (5), etc.
        

---

### 3\. Interactive Visual Analytics

**Methodology:** To analyze geographical patterns and create an interactive user experience, `Folium` was used for map visualizations and `Plotly Dash` was used to build an interactive dashboard.

#### Folium Map Analysis

- **Task 1: Mark all launch sites on a map.**
    
    - **Insight:** All launch sites are located in very close proximity to the coast (e.g., CCAFS SLC-40 is ~0.51 km away). This is likely for safety, allowing rockets to fly over the ocean during ascent. Not all sites are near the equator; VAFB SLC-4E is significantly further north, suggesting it is used for polar orbits.
        
- **Task 2: Mark success/failed launches for each site on the map.**
    
    - **Insight:** Using `MarkerCluster`, the map visually groups launches by site. The KSC LC-39A site shows a high concentration of green (success) markers, indicating a high success rate.
        

#### Plotly Dash Dashboard Analysis

An interactive dashboard was built allowing users to filter launch data by site and payload mass range.

- **Insight 1 (Pie Chart):** When filtering for the **KSC LC-39A** launch site, the pie chart shows it has a **76.9% success rate** (Class 1), the highest of all sites.
    
- **Insight 2 (Scatter Plot):** The dashboard's payload scatter plot (filtered for all sites) reveals that the **'FT' booster version** is used frequently and has a high success rate across various payload masses. It also confirms that there is no clear negative correlation between higher payload mass and landing success, suggesting other factors (like booster version) are more significant.
    

---

### 4\. Predictive Analysis (Machine Learning)

**Methodology:** The final module involved building classification models to predict landing success (the `Class` variable).

1. **Feature Engineering:** Categorical features like `Orbit` and `LaunchSite` were one-hot encoded.
    
2. **Standardization:** The entire feature set was standardized using `StandardScaler`.
    
3. **Train/Test Split:** The data was split into training and test sets (80/20 split).
    
4. **Model Tuning:** `GridSearchCV` was used to find the optimal hyperparameters for four different classification models: Logistic Regression, Support Vector Machine (SVM), Decision Tree, and K-Nearest Neighbors (KNN).
    

#### Model Performance Results

After tuning, each model was evaluated on the unseen test data. The **Decision Tree Classifier** achieved the highest accuracy.

| Model | Training Accuracy (GridSearchCV) | Test Accuracy |
| --- | --- | --- |
| Logistic Regression | 84.64% | 83.33% |
| Support Vector Machine (SVM) | 84.82%| 83.33% |
| **Decision Tree** | **94.44%** | **90.36%**| 
| K-Nearest Neighbors (KNN) | 84.82%| 83.33%|

Export to Sheets

#### Best Model Analysis: Decision Tree

- **Accuracy:** The optimized Decision Tree model was the most effective predictor, achieving **94.44% accuracy** on the test set.
    
- **Confusion Matrix:** The model performed exceptionally well, with **0 False Negatives** (it correctly identified all actual successful landings) and only 1 False Positive. This is crucial for a cost-estimation model, as failing to predict a success (a False Negative) would be more costly than incorrectly predicting one.
    

## Conclusion

This project successfully demonstrated a complete data science pipeline, from data acquisition to predictive modeling, to analyze SpaceX's Falcon 9 launch success.

The analysis revealed several key findings:

1. **Launch Site is a Factor:** The KSC LC-39A launch site has the highest success rate at 76.9%. All sites are located near the coast for safe trajectories.
    
2. **Booster Version is Key:** The 'FT' booster version shows high reliability across different payload masses.
    
3. **Payload Mass is Not Determinative:** No clear pattern links higher payload mass to a lower success rate; other variables like booster type and orbit are more significant predictors.
    
4. **Prediction is Highly Feasible:** A Decision Tree classification model was developed that can predict landing success with **94.44% accuracy**, providing a reliable tool for estimating launch costs.
    

The insights and models generated can aid stakeholders in making informed, data-driven decisions in the competitive space launch industry.

## Repository Structure

- **/data/**: Contains the dataset (`spacex_launch_geo.csv`) and related data scripts.
    
- **/notebooks/**: Includes the Jupyter notebooks documenting each module of the project (EDA, SQL, Folium, Plotly, ML).
    
- **/scripts/**: Contains Python scripts used for data processing, visualization, and modeling.
    
- **/dash\_app/**: Contains the code for the interactive Plotly Dash application.
    
- **README.md**: This comprehensive project overview.