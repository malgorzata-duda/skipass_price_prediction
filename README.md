# Prediction of skipass prices in European ski resorts using regression methods

# Comparison of Linear, Ridge and LASSO regression methods using multicollinear predictors

## Project Overview
The purpose of the project is to create a model predicting skipass prices in European ski resorts based on the resort's characteristics, such as e.g. highest point altitude, length of ski slopes, number of lifts and its location. 
A large subset of predictor variables exhibits multicollinearity, which violates the assumptions of valid OLS model estimation. Therefore, in addition to classical OLS method, regularization methods (such as Ridge and LASSO) were used, as they allow for estimation in the presence of multicollinear predictors. Results obtained using these methods were then compared and the final best-performing model was selected and interpreted. 

## Data

### Data Source

Data used in the project comes from the Kaggle website and involves information gathered from ski-resort-stats.com website in February 2022. Link to the dataset: https://www.kaggle.com/datasets/thomasnibb/european-ski-resorts/data. 

### Data Preprocessing
The dataset used in the analysis exhibited strong, and in some cases almost perfect multicollinearity. Moreover, some of the variables were skewed. The dataset also included some invalid records, which were identified and eliminated. 
In order to mitigate the impact of extreme outliers present in the dataset, a logarithmic transformation was applied to the skewed variables. In case of the variables including zero values, a *log1p* transformation was used, which calculates natural logarithm of (X+1).  
The problem of multicollinearity in the dataset was addressed by two approaches. First, a subset of predictors was selected by elimintating almost perfectly correlated variables. That approach allowed to weaken the strength of multicollinearity in the dataset, but did not completely mitigate the problem. Therefore, in the estimation process a regularization methods were used, which allowed to shrink the coefficients towards zero and make their estimation more stable.


## Objectives
The purpose of the project was to build a regression model to predict skipass prices in European resorts. A low prediction error and fitness on the test dataset were prioritized when selecting the best model. Another aim of the analysis was to identify the contribution of each variable to the ski pass prices. 

## Methodology
The project compares 3 regression methods: OLS, Ridge and LASSO regression. In case of regularization methods, a selection of $\alpha$ parameter value, controlling the strength of regularization, was performed by using cross-validation. In order to minimize the prediction error, the metric prioritized in the model selection process was **RMSE**. Final model was analyzed using individual SHAP profiles. 

## Results

### Model Performance and Final Model Selection

<img width="812" height="274" alt="errors_comparison" src="https://github.com/user-attachments/assets/04000ab3-8a0b-4b09-9b8b-be137d671722" />


<img width="436" height="274" alt="r2_comparison" src="https://github.com/user-attachments/assets/eb98ee86-4154-40ff-acc8-c8562704e833" />

All 5 of the estimated models performed very similarly. The models make prediction errors (RMSE) around 6.3 euros on the train and 6.8 on the test dataset (while a mean skipass price is 42 euros). Models explain approximately 60% of the variation of the skipass prices. Metrics' values vary only slightly between the train and the test dataset which suggests that there is no indication of model overfitting.
The results demonstrate the effectiveness of the regularization regression models for estimation in the face of severe multicollinearity. **The LASSO model, estimated on the limited set of predictors, was selected as the final model, due to its best performance on the test dataset and better interpretability (LASSO performs feature selection, while Ridge does not).** However, it is worth noting that the differences between the models' performances were almost negligible.

### Model interpretation

When interpreting the model coefficients, it is crucial to remember that for the *GondolaLifts* variable the *log1p* transformation was used instead of the standard logarithmic transformation. Therefore the provided interpretation of its coefficient is fully valid only when the values of the variable are relatively high. Moreover, as the numerical predictors were standardized, their coefficients refer to the change of one standard deviation of the variable.

| **Variable**           | **Coefficient** |
|------------------------|----------------:|
| **HighestPoint**       | 2.67        |
| **log_TotalSlope**     | 2.68        |
| **log1p_GondolaLifts** | 4.21        |
| **AlpineCountry**      | 3.06        |
| **PyreneanCountry**    | -5.86       |
| **NordicCountry**      | 9.58        |
| **Snowparks**          | 1.73        |
| **NightSki**           | -1.12       |
| **const**              | 39.34       |


* Ski pass prices in the Nordic countries are, on average, €9.58 higher than those in other countries, with other variables held constant. 
* Ski pass prices in the Alpine countries are, on average, €3.06 higher than those in other countries, with other variables held constant. 
* However, ski pass prices in the Pyrenean countries are, on average, €5.86 lower than those in other countries, with other variables held constant. 
* Resorts offering night skiiing have, on average, €1.12 lower ski pass prices than other resorts, with other variables held constant. 
* Ski pass prices in the resorts which offer ski parks are, on average, €1.73 higher than in other resorts, with other variables held constant.
* A one standard deviation rise of the altitude of the highest point in the resort is associated with a €2.67 rise of ski pass price.
* A one standard deviation rise of the logarithm of the total slope length is associated with a €2.68 rise of ski pass price.
* A one standard deviation rise of the logarithm (*log1p*) of the number of gondola lifts is associated with a €4.21 rise of ski pass price.

### Individual SHAP analysis

<img width="841" height="541" alt="shap_bialka" src="https://github.com/user-attachments/assets/92727d5a-6d91-4eeb-bbcc-87af41ed5508" />

The presented SHAP profile shows how, for the Bialka Tatrzanska resort, **a ski pass price of €27.8** was predicted. The strongest negative contribution (-€4.6) to the prediction can be attributed to the **lack of gondola lifts in the resort**. A **highest point of the resort located on 910 m** lowers the price by €4.38. A **total length of slopes** (its log_value  = 2.8, total length = 16 km) also negatively contributes to the price (-€2.5). On the other hand, a predicted price is increased because of **the snow park** in the resort (€0.52) and the fact, that the resort is not located in the Pyrenees (€1.93). The final ski pass price prediction equals €27.8, which is way below the mean value of €41.9. 


<img width="933" height="544" alt="shap_verbier" src="https://github.com/user-attachments/assets/2e7bd02d-fe95-406d-8c51-d3ad7cd552a3" />

The presented SHAP profile shows how the ski pass price prediction of €61.12 was obtained for the Verbier 4 Vallees resort. Almost every resort feature positively impacts the ski pass price. The highest contribution (€8.64) can be attributed to the **high number of gondola lifts** in the resort (log1p value = 3, number of lifts = 19) and an **extremely high total length of slopes** (€5.19. log value = 6, total length = 412 km). A high **location over 3,300m** also increases the price (€4.95). A much smaller positive contribution can be attributed to the resort's Alpine location (€0.43) and presence of snowparks (€0.52). The only features negatively contributing to the price are nightskiing (-€0.64) and location outside of Nordic countries (-€0.38). The final ski pass price prediction of €61.12 is very high, reflecting the resort's luxurious profile. 

## Potential applications

The estimated regression model may be valuable in various different applications. 
* Firstly, it can allow to **assess an appropriate price for the potential new ski resorts** in Europe, based on their characteristics. 
* Secondly, it allows to **estimate a suitable price shift** when changes in the existing ski resorts are introduced. For example, the resort's management can assess if a construction of a new gondola lift would be profitable, by comparing its cost to the potential income from the increased ski pass prices. 
* Lastly, the model, especially presented with the SHAP profiles, allows the customers to **understand and compare the prices** of the ski resorts and select the ski pass most suitable for their needs.

## Technologies
Python, pandas, NumPy, Matplotlib, Seaborn, statsmodels, scikit-learn, SHAP, KaggleHub, Jupyter Notebook

## Use of AI
AI tools were used as a supporting resource during the development of this project, mainly for debugging, explaining Python concepts, and improving the code. 
