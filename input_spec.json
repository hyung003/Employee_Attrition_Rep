"""
Henry Yung
HW5B

This is a main project script file.
"""
import logging
import os
import urllib
import pandas as pd
import numpy as np
import joblib


def s3_model_loader(url_path):
    """Fcn to begin testing prompt where user inputs values for all necessary
       features for the model which are then inserted into the model.
       Inputs:
           - url_path in s3 where the model is located
       Returns:
           - The model object
    """
    s3_model = joblib.load(urllib.request.urlopen(url_path))
    return s3_model


def json_file_processor(json_dir="input_spec.json"):
    """Fcn to begin testing prompt where user inputs values for all necessary
       features for the model which are then inserted into the model.
       Inputs:
           - path of the Json file. Using os.path on virtual environment to fetch data
       Returns:
           - A Dataframe of for the Json variables (Still need to convert before model prediction)
    """
    json_dat = pd.read_json(json_dir, orient='index')
    return json_dat


def convert_json_var(input_data):
    """Fcn that turn convert Json Input parameters for model use. It also check to ensure
        correct data types are used for each respective parameter.
        Model will not run unless data types are right.
       :type input_data: Json input
       :Argument:
           - inputs: array of inputs
       Returns:
           - Output of inputs ready for Model Prediction
    """

    if input_data['JobLevel'].dtype != 'int64' or \
            input_data['MonthlyIncome'].dtype != 'int64' or \
            input_data['YearsWithCurrManager'].dtype != 'int64' or \
            input_data['TotalWorkingYears'].dtype != 'int64' or \
            input_data['YearsInCurrentRole'].dtype != 'int64':
        return 'Error : Please enter integer64 value for either ' \
               'JobLevel, ' \
               'MonthlyIncome, ' \
               'YearsWithCurrManager, '\
               'TotalWorkingYears, ' \
               'YearsInCurrentRole'

    if input_data['JobInvolvement'].dtype != 'int64' or \
            input_data['StockOptionLevel'].dtype != 'int64' or \
            input_data['Age'].dtype != 'int64' or \
            input_data['YearsAtCompany'].dtype != 'int64' or \
            input_data['JobSatisfaction'].dtype != 'int64':
        return 'Error : Please enter integer64 value for JobInvolvement, ' \
               'StockOptionLevel, ' \
               'Age, ' \
               'YearsAtCompany, ' \
               'JobSatisfaction'

    if input_data['JobSatisfaction'].dtype != 'int64' or \
            input_data['EnvironmentSatisfaction'].dtype != 'int64':
        return 'Error : Please enter integer64 value for JobSatisfaction or EnvironmentSatisfaction'

    if input_data['OverTime'].dtype != 'object' or\
            input_data['MaritalStatus'].dtype != 'object' or \
            input_data['Gender'].dtype != 'object':
        return 'Error : Please enter string values- Overtime : Yes or No, ' \
               'MaritalStatus : Married, Single, Divorced, ' \
               'Gender: Male or Female'

    input_data['OverTime'] = np.where(input_data.OverTime == 'Yes', 1, 0)
    input_data['Gender'] = np.where(input_data.Gender == 'Male', 1, 0)
    input_data['MaritalStatus'] = np.where(input_data.MaritalStatus == 'Married', 1,
                                           np.where(input_data.MaritalStatus == 'Single', 2, 0))
    return input_data


def organize_json_input(input_data):
    """Function to organize json_input for model""
       Arguments:
           - input_dat: Json Dataframe
       Returns:
           - Dataframe with organize columns for prediction
    """
    # select relevant features for model
    model_vars = ['JobLevel', 'MonthlyIncome',
                  'YearsWithCurrManager', 'OverTime',
                  'TotalWorkingYears', 'YearsInCurrentRole',
                  'JobInvolvement', 'Age',
                  'StockOptionLevel', 'YearsAtCompany',
                  'MaritalStatus', 'JobSatisfaction',
                  'TrainingTimesLastYear', 'EnvironmentSatisfaction',
                  'Gender']
    output_data = input_data[model_vars]
    return output_data


logging.basicConfig(level=logging.INFO)
LOGGER = logging.getLogger(__name__)

if __name__ == '__main__':
    LOGGER.info("--- Part 1: Download Model FROM S3")

    CUR_PATH = os.getcwd()
    JSON_FILE = 'input_spec.json'
    JSON_PATH = os.path.join(CUR_PATH, JSON_FILE)

    LOGGER.info("--- Part 2: LOADING MODEL")
    S3_URL = 'https://stats404-project-henry-yung.s3-us-west-2.' \
             'amazonaws.com/rf_final_model.joblib'

    LOADED_MODEL = s3_model_loader(S3_URL)

    LOGGER.info("--- Part 3: Model loaded")
    LOGGER.info("--- Part 4: Running Test")

    # Load Json into DF
    JSON_DF = json_file_processor(JSON_PATH)

    # Convert Json inputs for Model
    JSON_DF = convert_json_var(JSON_DF)

    # Rearrange columns correctly for Model Input
    JSON_DF = organize_json_input(JSON_DF)

    # Test model
    PREDICT = LOADED_MODEL.predict(JSON_DF)

    if PREDICT == 0:
        print('0: No Employee Attrition')
    elif PREDICT == 1:
        print('1: Yes Employee Attrition')
    else:
        print('Please try again')

    LOGGER.info("--- Part 5: End of test")
