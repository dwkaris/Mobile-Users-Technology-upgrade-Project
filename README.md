# {DATA SCIENCE PROJECT}
##A notebook contain code on a data science project ,{DATE : 28/3/2020}
##  BY DUNCAN WACHIRA
### This is a detailed description of codes writen MTN CI to upgrade its technology infrastructure for its mobile users. 

# IMPORT PANDAS AND NUMPY
import pandas as pd
import numpy as np

# LOADING OUR DATASETS
# WE START WITH THE Cells_geo DATASET
# URL  [http://bit.ly/TelecomDataset1]
with open('cells_geo.csv','r') as f:
  cells_geo = pd.read_csv(f, index_col=0,encoding='utf',sep = ';')
cells_geo.head(5)

# WE LOAD THE Telecom_dataset DATASET
# URL [http://bit.ly/TelecomDataset1]
with open('Telcom_dataset.csv','r') as f:
  dataset1 = pd.read_csv(f, index_col='VALUE',encoding='utf',sep = ',')
dataset1.head(5)

# WE LOAD THE Telecom_dataset2 DATASET
# URL [http://bit.ly/TelecomDataset2]
with open('Telcom_dataset2.csv','r') as f:
  dataset2 = pd.read_csv(f, index_col='VALUE',encoding='utf',sep = ',')
dataset2.head(5)

# WE LOAD THE Telecom_dataset3 DATASET
# URL [http://bit.ly/TelecomDataset3]
with open('Telcom_dataset3.csv','r') as f:
  dataset3 = pd.read_csv(f, index_col='VALUE',encoding='utf',sep = ',')
dataset3.head(5)

#LETS CLEAN THE cells_geo DATAFRAME
# We start by droping the columns that we do not need
print(cells_geo.columns)
print("\n")
#We drop the unwanted columns
cells_geo_new = cells_geo.drop(['STATUS' , 'LATITUDE' , 'LONGITUDE','AREA','ZONENAME','LOCALISATION'], axis=1)
cells_geo_new.drop_duplicates(keep='last',inplace=True)
cells_geo_new.rename(columns={'SITE_CODE':'SITE_ID'}, inplace=True)
print(cells_geo_new.head(5))
print("\n")
# We check for null values in our dataset
print('check for null values in our dataset')
print(cells_geo_new.isnull().sum())

#LETS CLEAN THE Telcom_Dataset DATAFRAME
# We preview our columns
print(dataset1.columns)
print("\n")
# We drop the columns that we do not need
dataset1_new = dataset1.drop(['CELL_ON_SITE','DW_A_NUMBER_INT','DW_B_NUMBER_INT', 'COUNTRY_A', 'COUNTRY_B'], axis = 1)
#rename DATE_TIME  inorder to match other columns
dataset1_new.rename(columns={'PRODUTC':'PRODUCT' ,'DATETIME':'DATE_TIME'}, inplace=True)
print(dataset1_new.head())
print("\n")
#we fill null values in the site_ID with the values of the cell_ID
print('check if there are any null values in our dataset1_new dataframe')
print(dataset1_new.isnull().sum())
print("\n")
#we drop rows with missing values
dataset1_new.dropna(axis=0,how='any',inplace=True)
# We check if there are any null values in our dataset1_new dataframe
#print('check if there are any null values in our dataset1_new dataframe')
print(dataset1_new.isnull().sum())

#Clean dataset2
#Preview our columns
print(dataset2.columns)
print("\n")
# WE DROP THE UNWANTED COLUMNS 
dataset2_new = dataset2.drop(['CELL_ON_SITE','DW_A_NUMBER','DW_B_NUMBER', 'COUNTRY_A', 'COUNTRY_B'], axis = 1)
print(dataset2_new.head())
print('\n')
#We check for null values in our dataframe
print("display chek for missing values")
print(dataset2_new.isnull().sum())
print('\n')
#DROP ROWS WITH MISSING VALUES
dataset2_new.dropna(axis=0, how = 'any', inplace = True)
#CHECK IF NULL VALUES HAVE BEEN FILLED
print(dataset2_new.isnull().sum())

#CLEAN Telcom_Dataset3
#Lets preview our columns
print(dataset3.columns)
#We drop columns that we will not use
dataset3_new = dataset3.drop(['CELL_ON_SITE','DW_A_NUMBER_INT','DW_B_NUMBER_INT', 'COUNTRY_A', 'COUNTRY_B'], axis = 1)
print(dataset3_new.head())
print("\n")
#E CHECK FOR NULL VALUES
print(dataset3_new.isnull().sum())
print('\n')
#Rename column names
dataset3_new.rename(columns={'SIET_ID':'SITE_ID', 'CELLID':'CELL_ID' }, inplace=True)
#Drop columns with missing values
dataset3_new.dropna(axis = 0, how = 'any',inplace = True)
#CHECK IF NULL VALUES HAVE BEEN FILLED
dataset3_new.isnull().sum()

# MERGING THE DATASETS1, DATASETS2 and DATASETS3
combined_dataset = pd.concat([dataset1_new,dataset2_new,dataset3_new])
combined_dataset.drop_duplicates(keep='last',inplace=True)
combined_dataset

# WE SPLIT THE DATE_TIME COLUMN TO FORM DATE AND TIME SEPATE COLUMNS
combined_dataset['DATE']= combined_dataset['DATE_TIME'].str.split(" ").str[0] 
combined_dataset['TIME']= combined_dataset['DATE_TIME'].str.split(" ").str[1]
combined_dataset

# WE MERGE THE Cells_geo table and the combined_dataset table
# AND REMOVE DUPLICATES
combined_new = pd.merge(cells_geo_new,combined_dataset, how='inner',left_on='SITE_ID',right_on='SITE_ID')
combined_new=combined_new.drop_duplicates()
combined_new = combined_new.rename({'VILLES':'CITY'}, axis=1)
combined_new.head()

# WE START WITH OUR ANALYSIS
#Which ones were the most used city for the three days?
grouped_city = combined_new['SITE_ID'].groupby(combined_new['CITY'])
grouped_city.count().sort_values(ascending = False)

#least used city for the three days
grouped_city = combined_new['SITE_ID'].groupby(combined_new['CITY'])
grouped_city.count().sort_values(ascending = True)

# Most used  and least used region
grouped_region = combined_new['SITE_ID'].groupby(combined_new['REGION'])
grouped_region.count().sort_values(ascending = False)

#Most used product
grouped_product = combined_new['SITE_ID'].groupby(combined_new['PRODUCT'])
grouped_product.count().sort_values(ascending = False)

#MOST USED ZONE
grouped_Zone = combined_new['SITE_ID'].groupby(combined_new['DECOUPZONE'])
grouped_Zone.count().sort_values(ascending = False)
