# Anomaly Detection: Credit risk

The purpose of this experiment is to demonstrate how to use Azure ML anomaly detectors for anomaly detection. Anomaly detection is the process of detecting outliers in the data. Typical examples of anomaly detection tasks are detecting credit card fraud, medical problems or errors in text. Anomalies are also referred to as outliers,  novelties, noise, deviations and exceptions. The data consists of 'normal' applications and 'risky' applications. Risky transactions are assumed to be anomalous and dissimilar from the training data. We compare One-Class Support Vector Machine based and PCA-based anomaly detectors to distinguish between the two classes.

The most notable difference compared to supervised classification is that the training data consists of only a single category or class corresponding to normal data. Any labels present in the data are ignored during training. This is in contrast with supervised training where data from both positive and negative classes will be used. The scores generated by the anomaly detectors indicate a measure of distance from the 'normal' category of data. The scores can be uncalibrated and should not be interpreted as probabilities. The threshold for detection can be chosen by inspecting the ROC curve in the evaluate module.

![][image8]

## Data
Training data consists of credit card applications. The dataset consists of 1000 entries with 20 feature columns and 1 label column. The features are a mix of numeric and categorical types. The original data and the description of individual columns can be found in the [UCI data repository](https://archive.ics.uci.edu/ml/machine-learning-databases/statlog/german/).  In order to train the anomaly detectors we will be relying only on the 'normal' category (label value = 1) while ignoring the 'risky' category (label value = 2). However, we will be using both categories for evaluating the detector.

**If you change this experiment for your specific problem that has different labels for normal and abnormal samples, make sure you change the "Regular expression" condition accordingly in the two Split Data modules below that select rows containing 'normal' transactions.**

![][image1]

## Model

### Pre-process data
The first few steps consists of preparing the data for the downstream modules.

![][image2]

1. The target column 'Col21' is renamed as 'Label' and also marked as type 'label' using the __Metadata Editor__ module.
2.  We split the data into training (75%) and use the rest (25%) as test set.
3.  Anomaly detector train on data containing a single class (which is assumed to be 'normal'). We remove rows containing 'risky' label using a __Split__ module with a regex filter on the label column.
4. This procedure is repeated on the data used for parameter sweep (consisting of a further split of training data into train and validation sets).

### Learners
Currently, AzureML supports the following anomaly detectors.

 *  __One-Class Support Vector Machine__ : One class support vector machine implements a binary classifier where the the training data consists of examples of only one class (normal data). The model attempts to separate the collection of training data from the origin using maximum margin. By default, a radial basis kernel is used.

 * __PCA-based__: This algorithm uses PCA to approximate the subspace containing the normal class. The subspace is spanned by orthonormal eigenvectors associated with the top eigenvalues of the data covariance matrix. More details on PCA can be found [here](http://en.wikipedia.org/wiki/Principal_component_analysis). For each new input , the anomaly detector first computes its projection on the eigenvectors, then it computes the normalized reconstruction error. This norm error is the anomaly score. The higher the error, the more anomalous the instance is.

### Trainer

Anomaly detection has a separate trainer module since labels are optional during training. For all other supervised learning tasks, labels are required.

![][image3]

### Learner Parameters
The parameters of the learner can be specified in two different ways using the __Create Trainer Mode__ option found in the module properties.

![][image4]

 * __Single Parameter__ : In this mode, the parameters are specified manually.
 * __Parameter Sweep__: This mode is meant to be used in conjuction with the __Sweep Parameters__ module. Multiple values or a range of values can be specified for each of the tunable paramters. __Sweep Parameters__ module can then be used to optimize these values.

In the experiment, we use the __Parameter Sweep__ mode and specify a grid of value for each of the parameters. The parameter sweep is then executed to optimize F-score value of the detector. Other metrics relevant to classification such as AUC, ROC, precision/recall can also be optimized instead.

### Parameter Sweep
The parameter sweep takes an untrained learner, training data and optionally validation data. For the anomaly detector, we need to specify both training and validation data since the training data consists of examples from a single class whereas validation data consists of examples from both classes. This excludes the use of cross-validation to optimize paramters.

The __Sweep Parameters__ module outputs a learner with the  best settings. We can use this learner directly or re-train the learner with this setting using the entire training data. In this experiments, we do the latter.

### Scoring
Predictions from the anomaly detectors can be obtained using the generic __Score Model__ module. The output of one-class SVM anomaly detectors consists of uncalibrated scores that may be possibly unbounded. We normalize the scores to match the dynamic range of scores generated by the PCA anomaly detector (which lies in [0,1] range).

![][image6]

## Results
We use the generic __Evaluate__ Anomaly detectors can be evaluated on the same metrics as binary classifiers. Area under the ROC curve provides a good way to measure the discriminatory power of the anomaly detectors.

![][image7]

<!-- Images -->
[image1]:http://az712634.vo.msecnd.net/samplesimg/v1/31/dataset.PNG
[image2]:http://az712634.vo.msecnd.net/samplesimg/v1/31/preprocess.PNG
[image3]:http://az712634.vo.msecnd.net/samplesimg/v1/31/trainmodule.PNG
[image4]:http://az712634.vo.msecnd.net/samplesimg/v1/31/parameter-range.PNG
[image5]:http://az712634.vo.msecnd.net/samplesimg/v1/31/parameter-sweep.PNG
[image6]:http://az712634.vo.msecnd.net/samplesimg/v1/31/retrain.PNG
[image7]:http://az712634.vo.msecnd.net/samplesimg/v1/31/roc.PNG
[image8]:http://az712634.vo.msecnd.net/samplesimg/v1/31/experiment.PNG