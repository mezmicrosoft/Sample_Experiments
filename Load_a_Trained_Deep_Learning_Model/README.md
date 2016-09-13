# Summary #
This Collection includes a training experiment and a predictive experiment with a retraining web service and a predictive web service, respectively. This Collection demonstrates the usage of the **Load Trained Model** module to allow an easy request to a trained model.


# Load a Trained Deep Learning Model #
In the Collection, we include a training experiment and a predictive experiment to demonstrate a scenario where we can conditionally select a right trained model based on each prediction request.

- [Training Experiment](#training-experiment) We train a **Deep Learning** model with **Convolutional Neural Network** and **Max Pooling** techniques to recognize hand-written digits. We use the [Net#](http://azure.microsoft.com/en-us/documentation/articles/machine-learning-azure-ml-netsharp-reference-guide) language to define the advanced network architecture. A retraining web service is deployed to produce new trained model (an `.ilearner` file) for each input data set.

- [Predictive Experiment](#predictive-experiment) We utilize the **Load Trained Model** module to retrieve the previously trained model from various data sources. The predictive web service can selectively use the right trained model based on a request parameter to produce different prediction results.


## <a name="dataset"></a> Dataset ##
The data used in this experiment is a popular MNIST dataset which consists of 70,000 grayscale images of hand-written digits. The dataset is publicly available on the [MNIST website](http://yann.lecun.com/exdb/mnist) that has a training set of 60,000 examples, and a test set of 10,000 examples. Each row represents a 28 x 28 dense image with its represented digit (0-9 in the *Label* column).

In order to generate multiple trained models based on different training datasets (see details in the [Generating trained models](#generate) section below), we sample the original training set with replacement to create two other similar sets of training data with 60,000 records in each set.


## <a name="training-experiment"></a> Training Experiment ##

### Developing a multiclass classification model ###
In this experiment, we use the **Multiclass Neural Network** module to train a deep learning model, as it has several layers including convolutional, max pooling, and fully connected layers. To implement this complex neural network, we need to define a custom _Net#_ script to specify the hidden layers. We also adjust other parameters, such as **the Learning rate**, **number of learning iterations**, **the initial learning weights diameter**, **the momentum** and **the type of normalizer** to improve the model performance. To learn more about how to configure a neural network model, feel free to check out the [module documentation](https://msdn.microsoft.com/library/azure/e8b401fb-230a-4b21-bd11-d1fda0d57c1f).

A complex deep learning model may take hours to train. In order to reduce the running time for this experiment, we only construct 4 hidden layers (2 convolutional layers, 1 max pooling layer and 1 fully connected layers) and limit the number of learning iterations to 30. With the configuration in the figure below, we are able to predict the hand-written digits with an accuracy of 98.38%. To obtain a model with higher performance, we may try to construct more hidden layers and increase the number of learning iterations. The running time may be expected to increase accordingly.
![][image1]

To calculate the model accuracy based on the evaluation results, we create a custom R script in the **Execute R Script** module. The training experiment should look like the following figure.  
![][image2]


### Creating a retraining web service ###
Once we run the training experiment, we can publish it as a retraining web service by hovering over **SET UP WEB SERVICE** and clicking on **Retraining Web Service**.
![][image3]

This will transform our experiment and add web service input and output modules. As you can see in the below figure, two web service output modules are added. The one we are really interested in our case is the web service output from the **Train Model** module.
![][image4]


### <a name="generate"></a> Generating trained models ###
With the retraining web service we just deployed, we can easily produce a new trained model (an `.ilearner` file) for each training set. Clicking on **Batch Execution** under the *API Help Page* panel on the web service dashboard, we can find examples to submit a batch request to retrain the model. By utilizing the three training datasets we introduced in the [Dataset](#dataset) section, we submit three batch requests to generate three `.ilearner` files as the trained models.

We save the `.ilearner` files, named `trained_model.ilearner`, `trained_model2.ilearner`, and `trained_model3.ilearner`, in a container (`nnmodelblob`) in the [Azure Blob Storage](https://azure.microsoft.com/en-us/services/storage/blobs/).


## <a name="predictive-experiment"></a> Predictive Experiment ##

### Using load train model within a predictive experiment ###
In the training experiment with a retraining web service, we can also set up a predictive web service by hovering over **SET UP WEB SERVICE** again and clicking on **Predictive Web Service [Recommended]**.
![][image5]

The predictive web service will automatically create a trained model module and add web service input and output modules as showing in the figure below.
![][image6]

Instead of running this predictive web service and using the static trained model in a web service API, we can drag the **Load Trained Model** module and replace the previous trained model. With this **Load Trained Model** module, we have the ability to dynamically select which trained model to use per request. This enables to save time in our operationalization as the web service remains unchanged and support any version of the trained model. It also allows reducing the cost of maintaining multiple web services.


### Configuring the Load Trained Model module ###
To configure this module, we select the data source and fill in the appropriates fields. We can retrieve the trained models from either *Web URL via HTTP* or *Azure Blob Storage*. If you select a Web URL as your data source, you will need to provide the URL of the trained model. The **Load Trained Model** module is meant to be used in batch execution service (BES). The time to load the model put the time threshold of request-response service (RRS) at risk, so you have to opt-in to enable support of RRS request. Hence, it is generally advisable to run the web service in batch execution mode (BES).

In our case, we select the *Azure Blob Storage* as the data source, which requests addition information to access, such as `Account Name`, `Account key` and `Path to container or directory or blob`. We mark the `Path to container or directory or blob` field as a web service parameter that is allowed to change and retrieve different models per request. We also enable the RSS, which may introduce unpredictable delays, to show a prediction result later by enter a specific image data and the path to a new trained model in the **Test** option on the web service dashboard.
![][image7]

After running the predictive experiment, the experiment should look like the following figure.
![][image8]


### Using the predictive web service ###
We can now make a request to the prediction web service and pass `Path to container or directory or blob` as a parameter to conditionally select which trained model to use for each request.

For testing purpose, we enter a specific image data and the path to a new trained model in the same Blob container (`nnmodelblob/trained_model2.ilearner`) in the **Test** option.
![][image9]

We can see that the model successfully predict the **Label** as 1.
![][image10]



<!-- Images -->
[image1]:https://raw.githubusercontent.com/mezmicrosoft/Sample_Experiments/master/Load_a_Trained_Deep_Learning_Model/image1.PNG
[image2]:https://raw.githubusercontent.com/mezmicrosoft/Sample_Experiments/master/Load_a_Trained_Deep_Learning_Model/image2.PNG
[image3]:https://raw.githubusercontent.com/mezmicrosoft/Sample_Experiments/master/Load_a_Trained_Deep_Learning_Model/image3.PNG
[image4]:https://raw.githubusercontent.com/mezmicrosoft/Sample_Experiments/master/Load_a_Trained_Deep_Learning_Model/image4.PNG
[image5]:https://raw.githubusercontent.com/mezmicrosoft/Sample_Experiments/master/Load_a_Trained_Deep_Learning_Model/image5.PNG
[image6]:https://raw.githubusercontent.com/mezmicrosoft/Sample_Experiments/master/Load_a_Trained_Deep_Learning_Model/image6.PNG
[image7]:https://raw.githubusercontent.com/mezmicrosoft/Sample_Experiments/master/Load_a_Trained_Deep_Learning_Model/image7.PNG
[image8]:https://raw.githubusercontent.com/mezmicrosoft/Sample_Experiments/master/Load_a_Trained_Deep_Learning_Model/image8.PNG
[image9]:https://raw.githubusercontent.com/mezmicrosoft/Sample_Experiments/master/Load_a_Trained_Deep_Learning_Model/image9.PNG
[image10]:https://raw.githubusercontent.com/mezmicrosoft/Sample_Experiments/master/Load_a_Trained_Deep_Learning_Model/image10.PNG
