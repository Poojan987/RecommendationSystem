# Recommendation System
### Problem

Recommendations are significant for the company’s sales and marketing campaigns. Promoting the product to potential customers is necessary because the amount spent on marketing has a higher chance of conversion. Recommendations are helpful for the companies like Amazon; they can suggest some complementary products which the user had bought earlier or added to a cart multiple times. Even if Netflix wants to engage its consumers more and more on its platform, they need to provide suggestions based on predicting the user’s taste.

Here the problem is to select which type of model to recommend, whether it is Collaborative Filtering, Content-Based Model, or Hybrid Model. (Combination of Both). We have tried to find the best model for it based on the slightest error.


### Solution
As we have different approaches as an option which includes 
#####  &nbsp;&nbsp;&nbsp; (A)Content Based Model: It suggests on the basis of activity of the user. (Likes, Products bought etc)
#####  &nbsp;&nbsp;&nbsp; (B)Colloborative Model: It suggests on the basis of the activity of all the users and relevancy of the user with that particular product/content.
#####  &nbsp;&nbsp;&nbsp; (C)Hyrbrid: Hybrid approach includes the combination of both of (a) & (b). 

We tried to find out Root Mean Squared Error to find out the best recommendation model. We found that hybrid model turns out to be the best model with least error.

### Implementation Steps
Work-flow of this project can be divided into 7 main steps. The following directory structure was used during the development of this: 

##### HDFS Directory Structure :

A) Directories you need to create:-
/RcomSys

\- &nbsp;&nbsp;&nbsp;&nbsp; /OriginalData (Contains u.data file which is -> User Item Rating)

\- &nbsp;&nbsp;&nbsp; /items (Contains u.item -> Item details )

\- &nbsp;&nbsp;&nbsp; JUser (Contains u.user -> User details )

\- &nbsp;&nbsp;&nbsp; /Genre (Contains u.genre -> Genre details )

B) Directories that will be automatically created:-
/RcomSys

\- &nbsp;&nbsp;&nbsp; /UserltemRating (Output of MapReduce Job -> Modifeid Dataset )

\- &nbsp;&nbsp;&nbsp; /ContentOut (Output of ContentBased.pig )

\- &nbsp;&nbsp;&nbsp; /CollabOut (Output of Collaborative.pig )

\- &nbsp;&nbsp;&nbsp; /HybridOut (Output of Hybrid.pig )

##### 1) Creating the required [MySQL]
We assumed that the user has stored his data on MySQL server. We stored our data on the MySQL server and then processed it further. 

##### 2) Storing Data on HDFS(Hadoop Distributed File System) [SQOOP]
We did our entire processing on the Hadoop Framework. We needed to place our data from the MySQL server to HDFS. We used SQOOP to perform this task. The command used to copy the data from MySQL server to HDFS is:
```sh
sqoop import --connect jdbc:mysql://localhost/moviedb --table movies --m 1 --fields-terminated-by '\t' --target-dir /RcomSys/OriginalData;
```

##### 3) Modifying Data [Map Reduce]

The originally downloaded data contains ratings of Movies which were rated by the user to the Movies he/she has viewed. However, this data does not contain any rating for the Movies which were not rated by user. In this step, our task is to assign 0 (Zero) rating to the Movies which were not rated by user.

```sh
hadoop jar DSModification.jar PDriver /RcomSys/OriginalData/u.data /RcomSys/UserltemRating
```
##### 4) Processing Data [Pig]

This step contains the implementation of the algorithm which we are using. We have used three models for generating recommendations. One Pig Script was made for each Model which takes Modified Data as input from HDFS. For every user, we recommended 5 Movies which were generated using the recommendation algorithm.

The output gotten frrom the previous step will be used as input for the Pig Scripts. Therefore, we need to set the Output directory of Step-3 as input directory for Step-4.

```sh
pig ContentBased.pig
pig Collaborative.pig
pig Hybrid.pig
```

##### 5) Further Processing/Querying Output [Hive]

In order to get more details about the Movies which we are Recommending to a user, we placed the output gotten from of Step-4 in Hive Tables. Using this, if we want to find which genre is most liked by a particular user, then we can write Hive queries on these tables to get the desired information.

All of the data (such as USer Ratings, User Details, Movie Details, etc) was placed in Hive Tables. This helped us query out and receive the desired output from the processed data.

The Hive Script - hivescript1.sql was used to create all the required tables. The command for executing the same is:
```sh
hive -f hivescript1.sql
```
##### 6) Placing the output on mongoDb [mongoDB and Hive]
We exported all the data in our Hive table into mongoDb database and created collections corresponding to each Hive table.

In this step we had to start mongoDb Server and run the Hive Script - hivescript2 to export data from Hive to mongoDb. First we placed the Hive Jars into Hive’s lib folder and then executed the following command.
```sh
hive -f hivescript2.sql
```

##### 7) Creating GUI [JSP and mongoDB]

The GUI was created using JSP over mongoDB.




#### Describe the results properly
We collected dataset from a trusted source website namely movielens where the data is about users ratings and also about movies' title, cast , realese data , & genre. There are 100,000 ratings from 1000 users on 1700 movies.


The ratings from range 3 to 5 is higher as compared to 1 to 2. We can see pie chart for the percentage distribution.
![alt text](https://github.com/Poojan987/RecommendationSystem/blob/main/img/pieChart.png?raw=true)


Here is the line chart where we had showed Number of ratings given by the users vs the user id. Here we can see that the avg number of rating is around 100 and maximum reaches around 737.
![alt text](https://github.com/Poojan987/RecommendationSystem/blob/main/img/lineChart.png?raw=true)

We had also made UI to show a list of movies having ratings which a specific user has done.
![alt text](https://github.com/Poojan987/RecommendationSystem/blob/main/img/ui.png?raw=true)
