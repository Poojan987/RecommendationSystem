# Recommendation System

### Implementation
Workflow: 
Work-flow of this project can be divided into 7 main steps. This work-flow was designed assuming the
environment that we will have to face in an industry. But first things first, follow the following directory
structure to stay away from confusions.


### Steps:
##### 1) Creating the required [MySQL]
We assumed that the user has stored his data on MySQL server. We stored our data on the MySQL server and then processed it further. 

##### 2) Storing Data on HDFS(Hadoop Distributed File System) [SQOOP]
We did our entire processing on the Hadoop Framework. We needed to place our data frmo the MySQL server to HDFS. We used SQOOP to perform this task. The command used to copy the data from MySQL server to HDFS is:
```sh
sqoop import --connect jdbc:mysql://localhost/moviedb --table movies --m 1 --fields-terminated-by '\t' --target-dir /RcomSys/OriginalData;
```

##### 3) Modifying Data [Map Reduce]

The original downloaded data contains ratings of Movies which were rated by the user to the Movies he/she
has viewed. But this data does not contain any rating for the Movies which were not rated by user. Our task in
this step is to assign 0 (Zero) rating to the Movies which were not rated by user. This step was performed
according to the requirements of the Algorithms we are using.

The code for Driver, Mapper and Reducer Classes of Map Reduce Job is attached with this report.
```sh
hadoop jar DSModification.jar PDriver /RcomSys/OriginalData/u.data /RcomSys/UserltemRating
```
##### 4) Processing Data [Pig]

This is the most important step in the whole project as it contains the implementation of the algorithm which
we are using. As stated above, we have used three models for generating recommendations. We wrote one
Pig Script for each Model which takes Modified Data as input from HDFS. For each of 948 users we
recommended 5 Movies which were generated following the Algorithms.

3 Pig Scripts corresponding to each Model has been attached with this report. Input data for these Pig scripts
will be the output of Map Reduce Job in Step-3. That is, we need to set the Output directory of Step-3 as input
directory for Step-4.

```sh
pig ContentBased.pig
pig Collaborative.pig
pig Hybrid.pig
```

##### 5) Further Processing/Querying Output

Component Used: Hive

For querying the Output (Recommendations- 5 per User) in order to get more details about the Movies which
we are Recommending to a user, we placed our output of Step-4 in Hive Tables. For eg; if you want to which
genre is most like by a particular user, then you can easily write Hive queries on these tables to get the desired
information.

We actually placed all our data, i.e. User Ratings, User Details, Movie Details, etc ; every thing on Hive Tables
so that we can easily Query out out desired output from this processed data.

A Hive Script (hivescript1.sql) has been attached with this report which will create all the required tables.
Command:
```sh
hive -f hivescript1.sql
```
##### 6) Placing the output on mongoDb [mongoDB and Hive]
As most of the companies these days are using mongoDb over MySQL to store their data because of its NoSQL
properties, we also exported all the data in our Hive table into mongoDb database creating collections
corresponding to each Hive table.

We need to start mongoDb Server and run the attached Hive Script (hivescript2) to export data from Hive to
mongoDb.

Command: First place the attached Hive Jars into Hive’s lib folder and then run the following command.
```sh
hive -f hivescript2.sql
```

##### 7) Creating GUI [JSP and mongoDB]

Depicting the industrial environment and to show our Output, we created a GUI using JSP over mongoDb.
Some Screen-shots from the GUI are give at the last.

Full web application is attached with this report.
