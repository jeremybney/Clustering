# Clustering
Kmeans and Rough Kmeans

## Large datasets
Make sure to predefine the dimensions that you want to use and drop those that you don't. Either use the DEL function or explicitly list the features. 

## Read in the data
Make sure there are no NaN or null values otherwise the clustering won't work. To ensure there are no null values, either use .fillna(0) or

    for name in features_:
      df[name] = \
        df[name].fillna(df[name].median())

Moreover, Rough Kmeans can only use DICT so will need to .to_dict(orient = 'list') function to get this to work. 

## PCA
Use this script to flattens clusters and visualize in 2D

## set the centroid
Can eiter explicitly type out the centroid you want, or just use the values run previously using regular kmeans. 

## elbow method

Use the plotting function at the end of the code for kmeans to determine the optimal number of clusters. 
