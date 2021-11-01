# Wafer-Fault-Detection-ML-Project

Due to file size constrains I have uploaded the Files on the Google Drive.

Google Drive Link - https://drive.google.com/drive/folders/1wQs-QUpi5B7EVMzmnSJPOQxqZNcRDnnQ?usp=sharing

Try the app - http://waferfaultdetectionaws-env-1.eba-djpr3n2p.us-east-1.elasticbeanstalk.com/

(Due To Free Tier limitations, i have currently terminated the app)

## Poblem Statement:  
The inputs of various sensors for different wafers have been provided. In electronics, a wafer (also called a slice or substrate) is a thin slice of semiconductor used for the fabrication of integrated circuits. The goal is to build a machine learning model which predicts whether a wafer needs to be replaced or not(i.e., whether it is working or not) based on the inputs from various sensors. There are two classes: +1 and -1. 

+1 means that the wafer is in a working condition and it doesn’t need to be replaced.

-1 means that the wafer is faulty and it needs to be replaced. 

## Data Description - 
The client will send data in multiple sets of files in batches at a given location. Data will contain Wafer names and 590 columns of different sensor values for each wafer. The last column will have the "Good/Bad" value for each wafer.

"Good/Bad" column will have two unique values +1 and -1.  

"+1" represents Good wafer.

"-1" represents Bad Wafer. 

Apart from training files, we also require a "schema" file from the client, which contains all the relevant information about the training files such as:
Name of the files, Length of Date value in FileName, Length of Time value in FileName, Number of Columns, Name of the Columns, and their datatype.

## Data Validation - 

In this step, we perform different sets of validation on the given set of training files.  
Name Validation- We validate the name of the files based on the given name in the schema file. We have created a regex pattern as per the name given in the schema file to use for validation. After validating the pattern in the name, we check for the length of date in the file name as well as the length of time in the file name. If all the values are as per requirement, we move such files to "Good_Data_Folder" else we move such files to "Bad_Data_Folder."

 Number of Columns - We validate the number of columns present in the files, and if it doesn't match with the value given in the schema file, then the file is moved to "Bad_Data_Folder."

 Name of Columns - The name of the columns is validated and should be the same as given in the schema file. If not, then the file is moved to "Bad_Data_Folder".

The datatype of columns - The datatype of columns is given in the schema file. This is validated when we insert the files into Database. If the datatype is wrong, then the file is moved to "Bad_Data_Folder".

Null values in columns - If any of the columns in a file have all the values as NULL or missing, we discard such a file and move it to "Bad_Data_Folder".

## Data Insertion in Database
 
1) Database Creation and connection - Create a database with the given name passed. If the database is already created, open the connection to the database. 

2) Table creation in the database - Table with name - "Good_Data", is created in the database for inserting the files in the "Good_Data_Folder" based on given column names and datatype in the schema file. If the table is already present, then the new table is not created and new files are inserted in the already present table as we want training to be done on new as well as old training files.     

3) Insertion of files in the table - All the files in the "Good_Data_Folder" are inserted in the above-created table. If any file has invalid data type in any of the columns, the file is not loaded in the table and is moved to "Bad_Data_Folder".
 
## Model Training - 

1) Data Export from Db - The data in a stored database is exported as a CSV file to be used for model training.

2) Data Preprocessing   
  
   a) Check for null values in the columns. If present, impute the null values using the KNN imputer.
   
   b) Check if any column has zero standard deviation, remove such columns as they don't give any information during model training.


3) Clustering - KMeans algorithm is used to create clusters in the preprocessed data. The optimum number of clusters is selected by plotting the elbow plot, and for the dynamic   selection of the number of clusters, we are using "KneeLocator" function. The idea behind clustering is to implement different algorithms
  To train data in different clusters. The Kmeans model is trained over preprocessed data and the model is saved for further use in prediction.

4) Model Selection - After clusters are created, we find the best model for each cluster. We are using two algorithms, "Random Forest" and "XGBoost". For each cluster, both the algorithms are passed with the best parameters derived from GridSearch. We calculate the AUC scores for both models and select the model with the best score. Similarly, the model is selected for each cluster. All the models for every cluster are saved for use in prediction.
 
## Prediction Data Description
 
Client will send the data in multiple set of files in batches at a given location. Data will contain Wafer names and 590 columns of different sensor values for each wafer. 
Apart from prediction files, we also require a "schema" file from client which contains all the relevant information about the training files such as:
Name of the files, Length of Date value in FileName, Length of Time value in FileName, Number of Columns, Name of the Columns and their datatype.
 
## Data Validation  - 
In this step, we perform different sets of validation on the given set of training files.  

1) Name Validation- We validate the name of the files on the basis of given Name in the schema file. We have created a regex pattern as per the name given in schema file, to use for validation. After validating the pattern in the name, we check for length of date in the file name as well as length of time in the file name. If all the values are as per requirement, we move such files to "Good_Data_Folder" else we move such files to "Bad_Data_Folder". 

2) Number of Columns - We validate the number of columns present in the files, if it doesn't match with the value given in the schema file then the file is moved to "Bad_Data_Folder". 

3) Name of Columns - The name of the columns is validated and should be same as given in the schema file. If not, then the file is moved to "Bad_Data_Folder". 

4) Datatype of columns - The datatype of columns is given in the schema file. This is validated when we insert the files into Database. If dataype is wrong then the file is moved to "Bad_Data_Folder". 

5) Null values in columns - If any of the columns in a file has all the values as NULL or missing, we discard such file and move it to "Bad_Data_Folder".  

## Data Insertion in Database 

1) Database Creation and connection - Create database with the given name passed. If the database is already created, open the connection to the database. 

2) Table creation in the database - Table with name - "Good_Data", is created in the database for inserting the files in the "Good_Data_Folder" on the basis of given column names and datatype in the schema file. If table is already present then new table is not created, and new files are inserted the already present table as we want training to be done on new as well old training files.     

3) Insertion of files in the table - All the files in the "Good_Data_Folder" are inserted in the above-created table. If any file has invalid data type in any of the columns, the file is not loaded in the table and is moved to "Bad_Data_Folder".

## Prediction 
 
1) Data Export from Db - The data in the stored database is exported as a CSV file to be used for prediction.

2) Data Preprocessing    
   
   a) Check for null values in the columns. If present, impute the null values using the KNN imputer.
   
   b) Check if any column has zero standard deviation, remove such columns as we did in training.

3) Clustering - KMeans model created during training is loaded, and clusters for the preprocessed prediction data is predicted.

4) Prediction - Based on the cluster number, the respective model is loaded and is used to predict the data for that cluster.

5) Once the prediction is made for all the clusters, the predictions along with the Wafer names are saved in a CSV file at a given location and the location is returned to the client.

### Language Used - Python

### Deployed on - AWS(Elastic Beanstalk) - https://www.knowledgehut.com/blog/cloud-computing/what-is-beanstalk and https://aws.amazon.com/elasticbeanstalk/

### Front End by - iNeuron

### Framework - Flask

### Tools - PyCharm

### Algorihtms - Random Forest Classifier and XGBoost Classifier

### Accuracy Metric - AUC Score

## Advantages of using AWS Elastic Beanstalk :-
 1.AWS Elastic Beanstalk is an easy-to-use service for deploying and scaling web applications and services developed with Java, .NET, PHP, Node.js, Python, Ruby, Go, and Docker   on familiar servers such as Apache, Nginx, Passenger, and IIS.
 
 2.You can simply upload your code and Elastic Beanstalk automatically handles the deployment, from capacity provisioning, load balancing, auto-scaling to application health       monitoring. At the same time, you retain full control over the AWS resources powering your application and can access the underlying resources at any time.
 
 3.There is no additional charge for Elastic Beanstalk - you pay only for the AWS resources needed to store and run your applications.
 
 4.Increased productivity - 
  Since all the provisions and operations regarding the infrastructure and managing the application are handled by the Elastic Beanstalk, you as the developer don’t have to       waste   your time or learn the skills.
 
 5.Auto Scaling - 
  Based on what the specific needs of your application are, Elastic Beanstalk will automatically scale your application up and down. For this, it uses its Auto scaling settings   that you can adjust to suit your needs.
 
 6.Resource Control - 
  For all the AWS resources like Amazon EC2 that are powering your application, you will have full control. This will help you take manual control of your application’s        infrastructure if and whenever you desire to do so.
  
  


