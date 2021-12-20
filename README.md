# Name-Murari Lal


# Data Science and Business Analytics Internship

# The Sparks Foundation # 'GRIPDEC21'

# Task 3:(a)Performing ‘Exploratory Data Analysis’ on dataset ‘SampleSuperstore’ 

# (b)As a business manager, trying to find out the weak areas where you can work to make more profit.

# (c)What all business problems we  can derive by exploring the data?

#importing Important Library#

import numpy as np # linear algebra
import pandas as pd # data processing, CSV file I/O (e.g. pd.read_csv)
import seaborn as sns
import warnings
warnings.filterwarnings('ignore')
import matplotlib.pyplot as plt

sample=pd.read_csv("D:SampleSuperstore.csv")
sample.head()

sample.shape

sample.info()

sample.describe()

# Checking the duplication in dataset


sample.duplicated().sum()

#removing the duplicate values from the data set

sample.drop_duplicates(inplace = True)

sample.head()

sample.nunique()

#Checking if there is any missing data

sample.isnull().sum()#there is no missing data

col=['Postal Code']
sample1=sample.drop(columns=col,axis=1)                  #Removing unneccessary columns from the data set

sample1.head() # new dataset

#calculating correlation between the numerical variables
sample1.corr()

# Covariance of columns
sample1.cov()

#there is no  strong correlation in any of them.

# Expolatory Data Analysis:# DATA VISUALIZATION


plt.figure(figsize=(16,8))
plt.bar('Sub-Category','Category', data=sample1)
plt.title('Category vs Sub Category')
plt.xlabel('Sub-Catgory')
plt.ylabel('Category')
plt.xticks(rotation=45)
plt.show()

observation: phone,Accessories Machine,Copeirs are at higher rate in subcategory.

sample1.hist(bins=50 ,figsize=(20,15))
plt.show();

# Count the total repeatable states
sample1['State'].value_counts()

sample1['City'].value_counts()

plt.figure(figsize=(15,15))
sns.countplot(x=sample1['State'])
plt.xticks(rotation=90)
plt.title("STATE")
plt.show()


data_sample1=sample1.select_dtypes(include=[np.number])

# Analysing using BoxPlot 

plt.figure(figsize=[16,10])
sns.set(style="whitegrid")
sns.boxplot(x="variable", y="value", data=pd.melt(data_sample1), width=1)

plt.show()

# from above it is clear that Sales and profit are showing outliers.

from plotly import __version__
import cufflinks as cf

from plotly.offline import download_plotlyjs,init_notebook_mode,plot, iplot
init_notebook_mode(connected=True)

cf.go_offline

#Removal of Outliers
def remove_outlier(dataset,k=4):
    for col in dataset.columns:
            if (dataset[col].dtype=="int64" or dataset[col].dtype=="float64"):
                mean = dataset[col].mean()
                global sample1     
                std = dataset[col].std()    
                outlier = [i for i in dataset[col] if (i < mean - k * std)]
                outlier = [i for i in outlier if (i > mean + k * std)]       
                sample1 = dataset.loc[dataset[col].isin(outlier)]

data_sample1=sample1.select_dtypes(include=[np.number])
data_sample1.plot(kind='box')

### category wise sales in each region

plt.figure(figsize=[12,8])
ax = sns.barplot(x="Region", y="Sales", hue="Category", data=sample1, palette="Blues")

# observation: maximum sales is done from technology followed by furnitures.

#counting ship mode segment wise

sns.catplot("Ship Mode", hue="Segment", data=sample1, kind="count", aspect=1, palette="Set1")

#obseravtion: Standard class is highly prefered from all ship mode.

#Segment wise sales in Each region
plt.figure(figsize=[12,8])
ax = sns.barplot(x="Region", y="Sales", hue="Segment", data=sample1, palette="RdBu")

#observation:maximum sales is from Home office followed by consumer.

#moving on analysis of data using stastistic.

grouped= sample1.groupby("City")

grouped

agg_sales=grouped['Sales'].agg(np.sum).sort_values(ascending=False).reset_index() # aggregated sales per city

agg_sales.head()

#observation:New york city has most sales followed by Los angles ,Seattle and San francisco.

agg_profit=grouped['Profit'].agg(np.sum).sort_values(ascending=False).reset_index() # aggegrated profit per city

agg_profit.head()

#observation:New york city has most profit followed by Los angles ,Seattle and San francisco.

agg_discount=grouped['Discount'].agg(np.sum).sort_values(ascending=False).reset_index() #aggregated discount per city

agg_discount.head()

#observation: Maximum Discount is given by Philadelphia followed by Houston,chicago.

avg_sales=grouped['Sales'].agg(np.mean).sort_values(ascending=False).reset_index() #average sales per city

avg_sales.head()

#observation: maximum average sales is from Jamestown followed by Cheyenne,Bellingham .


#Cities with lowest Average sales
avg_sales.tail()

avg_profit=grouped['Profit'].agg(np.mean).sort_values(ascending=False).reset_index() #average profit per city

avg_profit.head()

#observation: maximum average profit is also obtained by Jamestown followed by Independence.

#Cities with lowest Average profit
avg_profit.tail()

avg_dist=grouped['Discount'].agg(np.mean).sort_values(ascending=False).reset_index() # average discount per city

avg_dist.head()

avg_dist.tail() # city with lowest average discount

#observation: city like Kenner,Jonesboro,Jefferson City,Jamestown,Aberdeen gives zero discount.


#Cities having High Average Discounts
high_dist=avg_dist[avg_dist['Discount'] >=0.6]

#Cities having low Average Discounts
low_dist=avg_dist[avg_dist['Discount']==0]


#Cities having High Average Sales
high_sales=avg_sales[avg_sales['Sales']>400]

#Cities having low Average Sales
low_sales=avg_sales[avg_sales['Sales']<40]


#Cities having High Average Profit
high_profit=avg_profit[avg_profit['Profit']>100]

#Cities having low Average profit
low_profit=avg_profit[avg_profit['Profit']<0]

#Cities with High-Average-Discounts but Low-Average-Sales
merged= pd.merge(high_dist,low_sales, on=['City'],how='inner')
merged

#insights1: The above 8 cities though gives high discount but their sales are very low .So it is not profitable to give more
discount further .better is not to invest any more in these city.

#Cities with high Average Sales as well as Average Profit
merged2= pd.merge(high_sales,high_profit, on=['City'], how='inner')
merged2.head()

#Insights 2:Basically above city are hotspot from business point of view in terms of all aspects i.s sales,profit.
so doing business at theses places will be more profitable.

#Cities where Average Discount is less but Average Sales is High
merged3= pd.merge(low_dist,high_sales, on='City', how='inner')
merged3

#insight3: above 10 city makes maximum sales with zero discount so increasing more investment or
discount may increase profitability.


#Cities with high Average sales but low Average profit
merged4= pd.merge(high_sales,low_profit, on='City', how='inner')
merged4

fig, ax = plt.subplots(figsize = (10 , 6))
ax.scatter(sample1["Sales"] , sample1["Profit"])
ax.set_xlabel('Sales')
ax.set_ylabel('Profit')
ax.set_title('Sales vs Profit')
plt.show()

#insight4: the above city though have more sales but they are loosing i.e no profit so better not to more investment \
or shift business to other city and must found  reason for losses.


#Cities with high Average discount but low Average profit
merged5= pd.merge(high_dist,low_profit, on='City', how='inner')
merged5

# insight 5: The above 8 city incured losses though gives maximum discount.
#so this place is not suitable for the more business investment.

#Cities with low Average discount but High Average profit
merged6= pd.merge(low_dist, high_profit, on='City', how='inner')
merged6

#insight 6: the above 27 city though gives zero discount but obtain high amount of profit.so best suited city for business.

#State wise profit
plt.figure(figsize=[24,15])
ax = sns.barplot(x="State", y="Profit", data=sample1, palette="Set1",)
plt.xticks(rotation=90, fontsize=16)
plt.yticks(fontsize=15)
plt.title("States VS Profit",fontsize=24)
plt.xlabel("States",fontsize=20)
plt.ylabel("Profit",fontsize=20)
plt.tight_layout()

#observation:The above  plot  clearly shows, 'District of Columbia', 'Vermont','Wyoming' states are generating highest profits.
And States like 'Texas', 'Pennsylvania','Illinois','Arizona','Oregon','Colorado','Ohio' are generating losses.
Hence focus has to be given in such States.

#Category Wise profit in the whole country
plt.figure(figsize=[12,8])
ax = sns.barplot(x="Category", y="Profit", data=sample1, palette="Set2")

#Category wise Profit in Each Region
plt.figure(figsize=[12,8])
ax = sns.barplot(x="Region", y="Profit", hue="Category", data=sample1, palette="Set2")

#observation: Maximum profit is gained from Technology a category part.so lets explore more on it.

#Subcategory wise profit
plt.figure(figsize=[14,10])
ax = sns.barplot(x="Sub-Category", y="Profit", data=sample1, palette="magma")
plt.xlabel("Subcategory", fontsize=15)
plt.ylabel("Profit",fontsize=15)
plt.xticks(rotation=90)
plt.show()

#observation: from above visualization it is clear that Tables and Bookcases are moving down instead of profit
so useful measures should be taken to improve it.Copiers is doing best and Accesories ,phones are doing good.


data=sample1[(sample1['Category']=="Technology")] ##Entries with Category=Technology

data

# finding all the sales under technology

plt.figure(figsize=[14,10])
ax = sns.barplot(x="Sub-Category", y="Sales", data=data, palette="Blues")
plt.xlabel("Subcategories",fontsize=15)
plt.ylabel("Sales",fontsize=15)

observation: above plot clearly explain that maximum sales from technology is obtained from copiers followed by machines
    and least by Accessories.Accessories must be taken in account to improve it.

# finding profit incured from each sub-category under technology

plt.figure(figsize=[14,10])
ax = sns.barplot(x="Sub-Category", y="Profit", data=data, palette="Reds")
plt.xlabel("Subcategories",fontsize=15)
plt.ylabel("Profit",fontsize=15)

observation: maximum profit is obtained from copiers and also it has maximum sales Though Machine is having
    maximum sales but profit obtained fom it is least among all while from Accessories profit is comparatively batter
    though its sales is least but to enhance more profit most focus be done on Accessories.

# finding the Profit of each Subcategory under Technology for each Region

plt.figure(figsize=[12,8])
ax = sns.barplot(x="Sub-Category", y="Profit",hue="Region", data=data, palette="RdBu")
plt.xlabel("Subcategories",fontsize=15)
plt.ylabel("Profit",fontsize=15)

Observation:Maximum profit from Copiers is obtained from Centrak region followed by west and east.
    but in machine it is from east and in phone and accessores almost all are equivalent.

# finding Profit of each Subcategory as per the Ship-mode

plt.figure(figsize=[12,8])
ax = sns.barplot(x="Sub-Category", y="Profit",hue="Ship Mode", data=data, palette="RdBu")
plt.xlabel("Subcategories",fontsize=15)
plt.ylabel("Profit",fontsize=15)

observation: IN case of Copiers maximum profit is obtained from First class followed by standard class and in Accessories
    from same day while in phone almost all are equivalent and in machine it is from second class ship mode.

# Conclusion:

From the Above data Visualization we can see that in Which states and in which Category Sales 
and profits are High or less,We can improve in that States By Providing Discounts in prefered Range so that
Company and consumer both will be in profit.So For Deciding that Range we have to do some Technical Analysis.
One can Do it through Factor Analysis,or also can Do it throgh neural networks.
One thing to be noted is that while the superstore is incurring losses due to giving discounts on its products,
they can't stop giving discounts of their products. Most of the heavy discounts are during festivals, end-of-season
and clearance sales which are necessary so that the store can make space in their warehouses for fresh stock. 
Also, by incurring small losses, the company gains in the future by attracting more long term customers. 
Therefore, the small losses from discounts are an essential part of company's business.

