# exploratory-data-analysis-sales-data-

## Import all req libraries

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
import datetime

df=pd.read_csv(r'C:\Users\mobin\Desktop\salesanalysis\salesforcourse1.csv') ##load dataset

df.head(10)

df.tail()

## last row and last column to be remove as contains almost nun value 

df.drop(['index','Column1'], axis=1,inplace =True)

df= df.drop(34866)

df.info()

## Drop columns name year and month. furthure adding new columns name year and year-month
df['Date']=pd.to_datetime(df['Date'])
df=df.drop(['Year', 'Month'], axis=1)
df['Year']= df['Date'].dt.year
df['Year-Month']= df['Date'].dt.strftime('%Y-%m')

df.head()

df.head()

# adding new column names profit, profit margin and unit profit, unit profit margin
df['Profit']=df['Revenue']-df['Cost']
df['Profit Margin']= round(df['Profit']/df['Revenue']*100,2)
df['Unit Profit']= df['Unit Price']-df['Unit Cost']
df['Unit Profit Margin']= round(df['Unit Profit']/df['Unit Price']*100,2)
df

loss_df=df[df['Revenue']<df['Cost']]
loss= loss_df.groupby(['Year-Month', 'Country'])

loss['Profit'].sum().sort_values(ascending=True).head(10).plot.bar(title='Loss')

df.groupby(['Country', 'State'])['Revenue'].sum().sort_values(ascending=True).head(10).plot.barh(title= 'Total Revenue') # Country and state wise total revenue

country_list= df['Country'].unique().tolist() #list of all countries

us_state_list= df[df['Country']=='United States'].State.unique().tolist() #list of all states in US
france_state_list= df[df['Country']=='France'].State.unique().tolist() #list of all states in France
uk_list= df[df['Country']=='United Kingdom'].State.unique().tolist() #list of all states in United Kingdom
germany_state_list= df[df['Country']=='United State'].State.unique().tolist() #list of all states in Germany

products= df['Product Category'].unique().tolist() #list of all product categories
Accessories=df[df['Product Category']=='Accessories']['Sub Category'].unique().tolist() #list of all product under accessories
Clothing=df[df['Product Category']=='Clothing']['Sub Category'].unique().tolist() #list of all product under cloths
Bikes=df[df['Product Category']=='Bikes']['Sub Category'].unique().tolist() #list of all product under bikes

products

fig, axs= plt.subplots(ncols=2,nrows=2, figsize=(16,8))
fig.suptitle('Data of Countries')
sns.barplot(x=country_list, y=[df[df['Country']==i]['Revenue'].sum() for i in country_list] , ax=axs[0,0])
axs[0,0].set_title("revenue")

sns.barplot(x=country_list, y=[df[df['Country']==i]['Cost'].sum() for i in country_list] , ax=axs[0,1])
axs[0,1].set_title("Cost")

sns.barplot(x=country_list, y=[df[df['Country']==i]['Profit'].sum() for i in country_list], ax=axs[1,0])
axs[1,0].set_title("Profit")

sns.barplot( x=country_list, y=[df[df['Country']==i]['Profit Margin'].mean() for i in country_list] , ax=axs[1,1])
axs[1,1].set_title("Profit Margin")

def get_product_details(detail_type):
    revenue=[]
    #for products in df['Product Category'].unique():
    for products in df['Product Category'].unique():
        revenue.append(df.loc[df['Product Category'].isin([products])][detail_type].sum())
    return revenue

fig, axs = plt.subplots(ncols=2, nrows=2, figsize=(16, 8))

fig.suptitle('Data of Products')

sns.barplot(x=df['Product Category'].unique(), y= get_product_details('Revenue'), ax=axs[0,0])
axs[0,0].set_title("revenue")

sns.barplot(x=df['Product Category'].unique(), y=get_product_details('Cost'), ax=axs[0,1])
axs[0,1].set_title("Cost")

sns.barplot(x=df['Product Category'].unique(), y= [i-j for i,j in zip(get_product_details('Revenue'),get_product_details('Cost'))], ax=axs[1,0])
axs[1,0].set_title("Profit")

sns.barplot( x=df['Product Category'].unique() , y=[i / j for i, j in zip([i-j for i,j in zip(get_product_details('Revenue'),get_product_details('Cost'))], get_product_details('Revenue'))], ax=axs[1,1])
axs[1,1].set_title("Profit Margin")

## Revenue from the Product Category Bikes is the most , but so is the cost as a result of which the profit and profit margin from this category is not pretty impressive. but Accessories and clothing have pretty good profit and profit margins

category= df.groupby(['Product Category','Sub Category'])[['Revenue','Cost']].sum().reset_index()
category['Profits']= category['Revenue'] - category['Cost']
category['Profit Margins']=  round(category['Profits']/ category['Revenue'] *100)
category

category.plot(x='Sub Category',
        kind='bar',
        stacked=False,
        title='details of products',
        figsize=(20, 10))

g = sns.FacetGrid(df, row='Country', col='Product Category', height=4, aspect=1)
g.map_dataframe(sns.histplot, x="Revenue")

