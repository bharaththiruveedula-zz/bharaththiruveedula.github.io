---
layout: post
author: Bharath Thiruveedula
title: Let's guess who can survive in Titanic disaster
date: 2017-12-24 18:43:07 +0530
categories:
- datascience
- opensource
tags:
- kaggle
- opensource
---
I have been trying to start some work in datascience area for a long time. I read many tutorials on DS, but haven't any done any practical application. So this blog series concentrates on taking some problem set and solve it using different machine learning algorithms any explain the algorithm along the way.

First of all let's set the context first. The content of this blog series can concentrate on any part of data science pipeline. The below diagram shows exactly what I mean by data science pipeline. So this series focuses on data visualisation or just data mungling or how to run predictive models in production or any combination of all these.


<img src="https://raw.githubusercontent.com/bharaththiruveedula/bharaththiruveedula.github.io/master/public/img/data_science_pipeline.png" />

For this post, I took the titanic problem from kaggle. The problem is to predict the survivability of passenger in the great titanic disaster. This PS is a beginner level problem in Kaggle(for that matter in data science itself). So obviously in this post we aren't discussing about "end product" in data science pipeline.

By the way if you like reading in jupyter notebook style [here](https://github.com/bharaththiruveedula/MachineLearning/blob/master/kaggle/titanic/Titanic-Kaggle.ipynb) is the notebook of the below content.

### Data Ingestion ###

Data Ingestion is the way of feeding your data for further steps in the data science pipeling. In this case we get the training set and test set from [kaggle](https://www.kaggle.com/c/titanic/data). Download those sets and load into pandas dataframe:

~~~
# First of all import all libraries that we need in our application
import pandas as pd
import matplotlib as plt
%matplotlib inline
import numpy as np
import warnings
warnings.filterwarnings('ignore')

# Here is the code to load the data
train_dataset = pd.read_csv("train.csv")
test_dataset = pd.read_csv("test.csv")
~~~

### Data Wrangling and Munging ###
Data Munging is basically the hip term for cleaning up a messy data set. It is usually used in conjunction with another hip term 'data science' which is basically data analysis. So we just need to clean the data. So to do that we first analyse the data we got from kaggle.

~~~
print(train_dataset)
PassengerId	Survived	Pclass	Name	Sex	Age	SibSp	Parch	Ticket	Fare	Cabin	Embarked
0	1	0	3	Braund, Mr. Owen Harris	male	22.0	1	0	A/5 21171	7.2500	NaN	S
1	2	1	1	Cumings, Mrs. John Bradley (Florence Briggs Th...	female	38.0	1	0	PC 17599	71.2833	C85	C
2	3	1	3	Heikkinen, Miss. Laina	female	26.0	0	0	STON/O2. 3101282	7.9250	NaN	S
3	4	1	1	Futrelle, Mrs. Jacques Heath (Lily May Peel)	female	35.0	1	0	113803	53.1000	C123	S
4	5	0	3	Allen, Mr. William Henry	male	35.0	0	0	373450	8.0500	NaN	S
5	6	0	3	Moran, Mr. James	male	NaN	0	0	330877	8.4583	NaN	Q
.....
891 rows × 12 columns
~~~

As you can see it has 891 rows of data and 12 columns/features. Let's analyse the properties of each feature

~~~
print(train_dataset.describe())

PassengerId	Survived	Pclass	Age	SibSp	Parch	Fare
count	891.000000	891.000000	891.000000	714.000000	891.000000	891.000000	891.000000
mean	446.000000	0.383838	2.308642	29.699118	0.523008	0.381594	32.204208
std	257.353842	0.486592	0.836071	14.526497	1.102743	0.806057	49.693429
min	1.000000	0.000000	1.000000	0.420000	0.000000	0.000000	0.000000
25%	223.500000	0.000000	2.000000	20.125000	0.000000	0.000000	7.910400
50%	446.000000	0.000000	3.000000	28.000000	0.000000	0.000000	14.454200
75%	668.500000	1.000000	3.000000	38.000000	1.000000	0.000000	31.000000
max	891.000000	1.000000	3.000000	80.000000	8.000000	6.000000	512.329200
~~~

If you observe the age feature, the count is just 714 the others are missing values. So let's fill those values with median

~~~
train_dataset['Age'].fillna(train_dataset['Age'].median(), inplace=True)
~~~

Now check the properties of each feature again

~~~
print(train_dataset.describe())


PassengerId	Survived	Pclass	Age	SibSp	Parch	Fare
count	891.000000	891.000000	891.000000	891.000000	891.000000	891.000000	891.000000
mean	446.000000	0.383838	2.308642	29.361582	0.523008	0.381594	32.204208
std	257.353842	0.486592	0.836071	13.019697	1.102743	0.806057	49.693429
min	1.000000	0.000000	1.000000	0.420000	0.000000	0.000000	0.000000
25%	223.500000	0.000000	2.000000	22.000000	0.000000	0.000000	7.910400
50%	446.000000	0.000000	3.000000	28.000000	0.000000	0.000000	14.454200
75%	668.500000	1.000000	3.000000	35.000000	1.000000	0.000000	31.000000
max	891.000000	1.000000	3.000000	80.000000	8.000000	6.000000	512.329200
~~~

Now let's see visually is there any correlation between sex and survivability

~~~
survived_gender = train_dataset[train_dataset['Survived']==1]['Sex'].value_counts()
dead_gender = train_dataset[train_dataset['Survived']==0]['Sex'].value_counts()
df = pd.DataFrame([survived_gender,dead_gender])
df.index = ['Survived','Dead']
df.plot(kind='bar',stacked=True, figsize=(15,8))
~~~

<img src="https://raw.githubusercontent.com/bharaththiruveedula/bharaththiruveedula.github.io/master/public/img/titanic_bar_graph.png" />

It is obvious that majority of persons who survived are women. In the similar way, we can check whether the feature is relevant or not.

### Data model prediction ###

Let's try to build the prediction model using random forest algorithm . The Random Forest technique handles the overfitting problem you faced with decision trees. It grows multiple classification trees using the training set. At the time of prediction, each tree is used to come up with a prediction and every outcome is counted as a vote. For example, if you have trained 3 trees with 2 saying a passenger in the test set will survive and 1 says he will not, the passenger will be classified as a survivor. This approach of overtraining trees, but having the majority's vote count as the actual classification decision, avoids overfitting.

~~~
# Import the `RandomForestClassifier`
from sklearn.ensemble import RandomForestClassifier
features = train_dataset[["Pclass", "Age", "Sex", "Fare", "SibSp", "Parch", "Embarked"]].values
target = train_dataset["Survived"].values
forest = RandomForestClassifier(max_depth = 10, min_samples_split=2, n_estimators = 100, random_state = 1)
my_forest = forest.fit(features,target)
~~~

Oh wait! you might have got this error

~~~
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
<ipython-input-104-0f31af4a8107> in <module>()
      1 # Building and fitting my_forest
      2 forest = RandomForestClassifier(max_depth = 10, min_samples_split=2, n_estimators = 100, random_state = 1)
----> 3 my_forest = forest.fit(features,target)

/usr/local/lib/python2.7/dist-packages/sklearn/ensemble/forest.pyc in fit(self, X, y, sample_weight)
    245         """
    246         # Validate or convert input data
--> 247         X = check_array(X, accept_sparse="csc", dtype=DTYPE)
    248         y = check_array(y, accept_sparse='csc', ensure_2d=False, dtype=None)
    249         if sample_weight is not None:

/usr/local/lib/python2.7/dist-packages/sklearn/utils/validation.pyc in check_array(array, accept_sparse, dtype, order, copy, force_all_finite, ensure_2d, allow_nd, ensure_min_samples, ensure_min_features, warn_on_dtype, estimator)
    431                                       force_all_finite)
    432     else:
--> 433         array = np.array(array, dtype=dtype, order=order, copy=copy)
    434
    435         if ensure_2d:

ValueError: could not convert string to float: Q

~~~

If you see the error is due to incomplete data cleaning. So let's clean it.

~~~
train_dataset["Sex"][train_dataset["Sex"]=="male"] = 0
train_dataset["Sex"][train_dataset["Sex"]=="female"] = 1
train_dataset["Embarked"] = train_dataset["Embarked"].fillna("S")
# Convert the Embarked classes to integer form
train_dataset["Embarked"][train_dataset["Embarked"] == "S"] = 0
train_dataset["Embarked"][train_dataset["Embarked"] == "C"] = 1
train_dataset["Embarked"][train_dataset["Embarked"] == "Q"] = 2

~~~
same thing for test data too.

~~~

# Impute the missing value with the median
test_dataset.Fare[152] = test_dataset["Fare"].median()
test_dataset["Sex"][test_dataset["Sex"]=="male"] = 0
test_dataset["Sex"][test_dataset["Sex"]=="female"] = 1
test_dataset["Age"] = test_dataset["Age"].fillna(test_dataset.Age.median())
test_dataset["Embarked"] = test_dataset["Embarked"].fillna("S")
# Convert the Embarked classes to integer form
test_dataset["Embarked"][test_dataset["Embarked"] == "S"] = 0
test_dataset["Embarked"][test_dataset["Embarked"] == "C"] = 1
test_dataset["Embarked"][test_dataset["Embarked"] == "Q"] = 2
# Extract the features from the test set: Pclass, Sex, Age, and Fare.
test_features = test_dataset[["Pclass", "Age", "Sex", "Fare", "SibSp", "Parch", "Embarked"]].values
~~~

Now let's predict with the test data

~~~
# Make your prediction using the test set
my_prediction = my_forest.predict(test_features)
~~~

Now write the predicted data in the format what kaggle expects

~~~
# Create a data frame with two columns: PassengerId & Survived. Survived contains your predictions
PassengerId = np.array(test_dataset["PassengerId"]).astype(int)
my_solution = pd.DataFrame(my_prediction, PassengerId, columns = ["Survived"])
my_solution.to_csv("my_solution_one.csv", index_label = ["PassengerId"])
~~~

This is simplest way of solving the titanic problem. But we have to fine tune some parameter to get even more good score. In the next post in this series, let's look into importance data visualisation.
