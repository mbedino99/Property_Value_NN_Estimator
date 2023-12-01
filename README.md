# Property_Value_NN_Estimator
### _Note, any .ipynb in this repo that uses the tensor-flow or keras-tuner library was processed in Google Colab_

## Project Overview
This neural network model was trained to predict the value of single family homes in Dallas, TX by taking in a number of features such as number of bedrooms, square footage, and zip code. 

## Kaylee
  ### Data Aquisition
  - This model is trained on a list of addresses in Dallas, TX that were sourced from the [Dallas Central Appraisal District website](https://www.dallascad.org/). This downloadable csv was then cleaned to remove any rows with empty data, so that it was usable with an API. This cleaned dataset can be found in data/Addresses_cleaned.csv.
  - All data on the house features was obtained from [Redfin](redfin.com). The addresses from DCAD were reformatted to be used with a wrapper to access Redfin's unlisted API. This allowed access to the data without violating Redfin's terms of use against webscraping.
    - [Redfin API Wrapper](https://github.com/reteps/redfin)
    - The code used to pull this data is located in final_code/ETL.ipynb
      - Due to the extremely long length of time it takes to pull data using this method, this was done in batches of 10,000 addresses at a time. These batches were exported as csvs to the data folder and then concatenated.
      - Despite a list comprising over 600,000 addresses, time constraints permitted data extraction for only around 40,000 homes.
    
## Martin
  - Exploratory data analysis of our data to better understand the scope of the project.
    - Used visualization tools and statistical analysis to visualize and understand the shape and scope of our data, as well as performed statisatical analysis to understand the correlations between the variables in our data and our target variable of home prices.
    - Cleaned data in order to better account for outliers, visualize upper and lowe bounds as well as test for normality
  - Perform a preeliminary OLS regression of the coefficients in order to visualize the validity of our data for further tuning.
    - Test the data to ensure it clears the classical assumptions
      - we found a small but possible instance of autocorrelation
      - we found the data to be homoskedastic
      - there is no perfect linear relation between home prices and our explanatory variables
  - From this quick analysis we find a adj. r^2 value of 0.678, meaning there is room for improvement of the model through hyperparameter tuning 

## Nathan-Andrew Tompkins
  ### _code located in file: nn_model_tuning_2_
  ### Keras Tuner structure
  - Using the Keras-Tuner library our hyperparameter tuning function initializes a Sequential keras model and uses four separate activation functions to test on for the hidden layers: 'relu', 'tanh', 'linear', 'softplus'.
  - The tuner-function was set to choose between 2 and 20 layers, each comprised of up to 20 neurons each.
  - The output layer was designed to have a single output, which would be the predicted house price.
  - As this was not a classic example of a model designed to cluster or classify data and instead predict a numerical value close to the actual value, it became neccesary to find a proper loss and evaluation method.
    - Our loss function was the "Mean Absolute Percentage Error" function. This allowed for our model to test the data's accuracy within a certain percentage of the target instead of exacting values.
    - The MAE metric was used to determine the average value that the model was off in it's pricing estimation. (Note that this value is greater than expected due to the high variance of housing prices above 2.5 million.)
  - The tuner-hyperband itself is initialized using the tuning-function as the first parameter. The "objective" here is "val_loss" as the primary function of this model is to reduce the percentage error between predicted and target vales. Likewise, the model callback/checkpoint "mode" variable is looking for the minimal value of the loss function to determine which tested model performs the best.

  ### Final model structure
  - Due to unexpected errors when using the best model determined using the keras-tuner our final model uses only a few of those specifications. In the end, our model is Sequential and comprised of 4 layers:
    1. Input layer using the "relu" activaiton function
    2. Two hidden layers with 11 and 13 neurons respectively using the "linear" activation function.
    3. Output layer using the "linear" function.
   
  ### Model Results
  - **loss: 9.2879** Indicates that predicted housing prices are within ~9.3% of the target values.
  - **mae: 38710.2656**. Indicates that our data, on average, is off by ~38k. Again this is due to the high variance and low number of datapoints of houses above 2.5$ million.
  
## David
[Tableau Story](https://public.tableau.com/shared/XTXD43674?:display_count=n&:origin=viz_share_link)
Visualizations generated to illustrate full dataset as well as our cleaned dataset and our model's performance. Visualizations include price distribution maps, and datapoints by ZIP Code.

