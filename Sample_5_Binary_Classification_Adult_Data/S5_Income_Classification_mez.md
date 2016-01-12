# Binary Classification: Income Level Prediction

In this sample experiment we will train a binary classifier on the [Adult](http://archive.ics.uci.edu/ml/datasets/Adult) dataset, to predict whether an individual’s income is greater or less than $50,000. We will show how you can perform basic data processing operations, split the dataset into training and test sets, train the model, score the test dataset, and evaluate the predictions.

The complete experiment graph is given below.
![][image1]

## Creating the Experiment##
1. Drag and drop the **`Adult Census Income Binary Classification dataset`** module into your experiment's workspace.
2. Add a **Project Columns** module and use the column selector to exclude `education` because `education` and `education-num` duplicate the adults' education information. So we only keep one feature `education-num` in this experiment.
3. Use two **Clean Missing Data** modules to handle missing values in different features.  
	1. In the first **Clean Missing Data** module, we select columns `workclass` and `native-country` from column selector, choose _Custom substitution value_ from the Cleaning mode and enter text _Other_ in the Replacement value.
	![][image2]
	2. In the second **Clean Missing Data** module, we select columns `occupation` from column selector, choose _Custom substitution value_ from the Cleaning mode and enter text _Other-service_ in the Replacement value.
	![][image3]
4. Use **Metadata Editor** module to convert the string features to categorical features. In the column selector, we select these string columns: `workclass`, `marital-status`, `occupation`, `relationship`, `race`, `sex` and `native-country`. We also choose _String_ as Data type, _Make categorical_ in Categorical, and remain the rest options as default.
![][image4]
5. Add a **Split** module to create the testing and test sets. Set the *Fraction of rows in the first output dataset* to 0.7. This means that 70% of the data will be output to the left port and the rest to the right port of this module. We will use the left dataset for training and the right one for testing.
5. Add a **Two-Class Boosted Decision Tree** module to initialize a boosted decision tree classifier.
![][image5]
6. Add a **Train Model** module and connect the classifier (step 5) and the training set (left output port of the **Split** module) to the left and right input ports respectively. This module will perform the training of the classifier.
7. Add a **Score Model** module and connect the trained model and the test set (right port of the **Split** module). This module will make the predictions. You can click on its output port to see the actual predictions and the positive class probabilities.
8. Add an **Evaluate Model** module and connect the scored dataset to the left input port. To see the evaluation results, click on the output port of the **Evaluate Model** module and select *Visualize*.

## Results
From these results, you can see that the **Two-Class Boosted Decision Tree** is fairly accurate in predicting income for the `Adult Census Income` dataset.
![][image6]

<!-- Images -->
[image1]:https://raw.githubusercontent.com/mezmicrosoft/Sample_Experiments/master/Sample_5_Binary_Classification_Adult_Data/image1.PNG
[image2]:https://raw.githubusercontent.com/mezmicrosoft/Sample_Experiments/master/Permutation_Feature_Importance/image1.PNG
[image3]:https://raw.githubusercontent.com/mezmicrosoft/Sample_Experiments/master/Permutation_Feature_Importance/image2.PNG
