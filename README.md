#NBA Team Classifier
Investigate what factors distinguish the successful teams in the NBA and to create model to predict playoff sucess.

##Data Collection
Data was scraped from basketball-reference. Basic and advanced metric statistics were collected by scraping specific tables. The data was then cleaned so a single dataframe represented a single season where the columns were the various statistics, and the rows were the 30 NBA teams. Initially, when working with data from only the past 10 years, the data was collected manually by downloading the tables and then merging them. Labeling the data with each team's playoff result was particularly time consuming when done manually. However, eventually it became clear that collecting data from several years past would be much more beneficial. Thus a scraper was implemented to easily automate the process and allow for data collection from the last 50+ years.

##Initial Analysis
There were 58 different statistics used to measure each team during each season. Correlation Heat Maps were constructed to observe the relationships between the feature variables, as well as the change in correlation between features over time. For example the correlation between points scored and three pointers made rises steadily from 1980(0.1) to 1990(0.28) to 2000(0.45) to 2010(0.54) to 2015(0.65) where it then begins to level off.

##Principal Component Analysis
With 58 feature variables for each team it was important to determine which variables caused the largest variance between the different teams. In order to that principal component analysis (pca) was performed on the individual season data and then later on groups of seasons. To visually observe each team data point both 2 and 3 principal components were used. The resulting graphs showed that non-playoff teams were consistenly clustered quite well together and the 1st,2nd, and Conference Final teams are reasonably well clustered together. Principal Component 1 captured a majority of the variance (43% and 40%)

##Model Building (K-Nearest Neighbors)
Using K-Nearest Neighbors (knn), a classifier was used to predict the playoff results from the years 1980-2018. For this classifier it was determined that 5 neighbors produced the optimal results. The training data used was the original data with 58 features (not the PCA data). The training data consisted of data from years 1980-2018. Then 5 fold cross validation was performed to assess the model's ability to successfully predict unseen data. The mean score from the cross validation was 56%. It is worth noting that predicting every team to be 'DNQ' would yield ~46% accuracy.

##Model Building (Random Forest)
Next, a Random Forest classifier was used. Once again the training data used was the original data of 58 features consiting of the years 1980-2018. 5 fold Cross Validation was perfomered again. The mean score here was slightly higher at 61% and the most important feature in the classifier was SRS(margin of victory, adjusted for strength of schedule).

##Model Building (Manual Means Cluster)
What a noticed about the predictions from both the KNN and Random Forest classifiers was that certain labels were never issued when predicting one or even two seasons worth of data. A majority of the data points were labeled as DNQ, 1st Round, or 2nd Round. Many times, a champion would never be predicted. When predicting a single season, it is known how many teams fall into each 'cluster'. There are 14 DNQs, 8 1st Rounds, 4 2nd Rounds, 2 Conference Finals, and 1 Champion. I wanted to take this into account. Using the PCA data from 1980-2018 consiting of 5 columns : PC1, PC2, PC3, Year, Playoff Result, I found the cluster centroid for each of the labels (DNQ, 1st Round, etc.) When calculating this the test data was left out by drop the entries belonging to a certain year. Then the test data was introduced and for each team data point, a distance was calculated from that data point to all 5 cluster centroids. Finally the appropriate numberof data points, corresponding to the cluster size, were assigned to each label by finding the data points with the shortest distance to the centroids.

##Conclusions/Results
Three different classification models were used: KNN, Random Forest, and a manual Kmeans Cluster. Given that predicting every team as 'DNQ' would yield a ~46% accuracy, all the models scored quite low.
Here are the results from the various models:

* KNN (Standard Data): 56% Mean Accuracy from 5 Fold Cross Validation
* KNN (PCA Data): 55.7% Mean Accuracy from 5 Fold Cross Validation
* Random Forest : 61% Mean Accuracy from 5 Fold Cross Validation
* Manual Kmeans Cluster (DNQ First, Champion Last) : 58% Mean Accuracy from years 1996-2017
* Manual Kmeans Cluster (DNQ Last, Champion First) : 45% Mean Accuracy from years 1996-2017

Here is the success rate of picking the correct champion for each year:

* KNN (Standard Data): 8/38, 21%
* KNN (PCA Data): 2/38, 5%
* Random Forest: 5/38, 13%
* Manual Kmeans Cluster (DNQ First, Champion Last) : 0/22, 0%
* Manual Kmeans Cluster (DNQ Last, Champion First) : 14%

Here is an adjusted succes rate where the correct champion was picked or was labeled "Finals"

* KNN (Standard Data): 11/38, 29%
* KNN (PCA Data): 3/38, 8%
* Random Forest: 12/38, 31.5%
* Manual Kmeans Cluster (DNQ First, Champion Last): 1/22, 4.5%
* Manual Kmeans Cluster (DNQ Last, Champion First) : 3/22, 14%

The Random Forest Classifer outperformed the other models slightly overall, but the KNN (Standard Data) model was the best at predicting the correct Champion. Suprisingly the KNN Classifier trained and predicted on the Standard data performed better than whe used on the PCA data. While the manual Kmeans Cluster (DNQ First) was more accurate than both KNN models overall, it was the worst at predicting the champion. It actually got none correct. The manual Kmeans Cluster (Champion First) performed the worst overall and expectedly performed better at picking the Champion than Kmeans Cluster (DNQ First). However, many of the incorrect labels given by Kmeans Cluster (Champion First) were 'DNQ', which is as wrong a prediction as possible.