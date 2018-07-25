# Clustering
Kmeans and Rough Kmeans

# Import libraries
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.cluster import KMeans
from sklearn.preprocessing import StandardScaler
from sklearn import preprocessing
from scipy.spatial.distance import cdist

# Read in the data
df2 = pd.read_csv("/Users/jney/Desktop/NewClusters/May2018_v2.csv")
df2 = df2.fillna(0)
median_deposits = df2['deposits'].median(skipna=True)
df2['deposits']=df2.deposits.mask(df2.deposits == 0,median_deposits)
df2['percent_noncore'] = (100-(df2['core_pairs_traded']/df2['pairs_traded'])*100)
df2['vol/deposits'] = (df2['total_vol']/df2['deposits'])
df2['withdrawals/deposits'] = (df2['withdrawals']/df2['deposits'])

# Print the names of the columns
print(df2.columns)

#alternatively, can use features_ = df.columns.tolist() if want to use all columns
features = ['total_vol', 'trade_count', 'withdrawals/deposits', 'maker_vol', 'taker_vol', 'percent_noncore', 'loans', 'vol/deposits']
user = ['userid']
revenue = ['revenue']

#scale the dataframe
scaler = preprocessing.StandardScaler().fit(df2[features])
df_scaled2 = pd.DataFrame(scaler.transform(df2[features]), columns=features)

#create a kmeans model with parameters we want and fit to the df
kmeans_model = KMeans(n_clusters=6, init='k-means++', n_init=10, random_state = 123, max_iter=300)
kmeans_model.fit(df_scaled2[features])

#this will record the cluster centroids
centers_May18 = pd.DataFrame(kmeans_model.fit(df_scaled2[features]).cluster_centers_, columns = features)

#predict the clusters and then append the clustering and extra columns to the dataframe
df_scaled2['label'] = kmeans_model.predict(df_scaled2[features])
df_scaled2['userid'] = df2[user]
df_scaled2['revenue'] = df2[revenue]

#print the number of values in each cluster
print df_scaled2['label'].value_counts()

#export CSV
df_scaled2.to_csv("/Users/jney/Desktop/NewClusters/clusters/May2018_clusters_v2.csv", sep='\t')

# k means determine k
distortions = []
K = range(1,10)
for k in K:
    kmeanModel = KMeans(n_clusters=k).fit(df_scaled2)
    kmeanModel.fit(df_scaled2)
    distortions.append(sum(np.min(cdist(df_scaled2, kmeanModel.cluster_centers_, 'euclidean'), axis=1)) / df_scaled2.shape[0])
# Plot the elbow
plt.plot(K, distortions, 'bx-')
plt.xlabel('k')
plt.ylabel('Distortion')
plt.title('The Elbow Method showing the optimal k')
plt.show()
