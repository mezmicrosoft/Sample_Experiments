# Summary #
This Collection includes a training experiment and a predictive experiment with a retraining web service and a predictive web service, respectively. This Collection demonstrates the usage of the **Load Trained Model** module to allow an easy request to a trained model.


# Load a Trained Deep Learning Model #
In the Collection, we include a training experiment and a predictive experiment to demonstrate a scenario where we can conditionally select a right trained model based on each prediction request.

- [Training Experiment](#training-experiment) We train a **Deep Learning** model with **Convolutional Neural Network** and **Max Pooling** techniques to recognize hand-written digits. We use the [Net#](http://azure.microsoft.com/en-us/documentation/articles/machine-learning-azure-ml-netsharp-reference-guide) language to define the advanced network architecture. A retraining web service is deployed to produce new trained model (an `.ilearner` file) for each input data set.

- [Predictive Experiment](#predictive-experiment) We utilize the **Load Trained Model** module to retrieve the previously trained model from various data sources. The predictive web service can selectively use the right trained model based on a request parameter to produce different prediction results.


## Dataset ##
The data used in this experiment is a popular MNIST dataset which consists of 70,000 grayscale images of hand-written digits. The dataset is publicly available on the [MNIST website](http://yann.lecun.com/exdb/mnist) that has a training set of 60,000 examples, and a test set of 10,000 examples. Each row represents a 28 x 28 dense image with its represented digit (0-9 in the *Label* column).

In order to generate trained models (see details in the Generating )


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


### Generating trained models ###
Using the retraining web service we just developed, we can produce new trained models (`.ilearner` files) with different training datasets. Clicking on **Batch Execution** under *API Help Page* on the web service dashboard, we can find examples to submit a batch request to retrain the model. With this web service we can easily produce new trained model for each of our data set.  
3. Using load train model within a predictive experiment
In our predictive experiment, drag the “load trained model” module and replace the previous trained model.




## <a name="predictive-experiment"></a> Predictive Experiment ##



1. We will start with the most basic type of neural network: one hidden layer, fully connected. In the list of samples in Azure ML Studio, find the sample experiment, **`Neural Network: 1 hidden layer`**. Save a copy of the experiment to your workspace by clicking **Save As**. The experiment should look like the following graphic:  



<!-- Images -->
[image1]:https://raw.githubusercontent.com/mezmicrosoft/Sample_Experiments/master/Load_a_Trained_Deep_Learning_Model/image1.PNG
[image2]:https://raw.githubusercontent.com/mezmicrosoft/Sample_Experiments/master/Load_a_Trained_Deep_Learning_Model/image2.PNG
[image3]:https://raw.githubusercontent.com/mezmicrosoft/Sample_Experiments/master/Load_a_Trained_Deep_Learning_Model/image3.PNG
[image4]:https://raw.githubusercontent.com/mezmicrosoft/Sample_Experiments/master/Load_a_Trained_Deep_Learning_Model/image4.PNG
[image5]:https://raw.githubusercontent.com/mezmicrosoft/Sample_Experiments/master/Load_a_Trained_Deep_Learning_Model/image5.PNG
[image6]:https://raw.githubusercontent.com/mezmicrosoft/Sample_Experiments/master/Load_a_Trained_Deep_Learning_Model/image6.PNG
[image7]:https://raw.githubusercontent.com/mezmicrosoft/Sample_Experiments/master/Load_a_Trained_Deep_Learning_Model/image7.PNG
