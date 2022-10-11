# Transfer_learning_Templates_application
This repo. Is intended for constructing a pipeline template for transfer learning applications.  Transfer learning is a technique which allows replacing of the hidden layer parameters in an AI training process where the input sample size is small.  The outcome of the training results from a small sample size is generally either biased or performs badly.  This repo. Introduces a template construct which allows the replacement action from a large data set such as Vgg-16 or Resnet.  Users don’t have to rewrite the transfer learning code and replace the big data hidden layers  manually.  After the transfer learning template is developed, it can be uploaded and shared on the Kubeflow pipeline UI.  A Pipelines component is a set of independent user code, packaged as a Docker image, that executes a step in a pipeline. For example, a component can be responsible for data preprocessing, data transformation, model training, etc., implemented as a Kubernetes CRD (Custom Resource Definition). So workflows can be managed using kubectl and integrated with other Kubernetes services such as volumes, secrets, and RBAC.
The test data set used in this repo. Is from voice speech recognition application.  The voice patterns of three persons is converted in frequency spectrum via fourier transformation as the following example.
 The transformed spectrum is reduced to a 100*100 image as shown below.  There are three categories of images from three different persons.  An eight twenty ratio of data division is used for training and testing of the proposed transfer learning template examples.  

![image](https://user-images.githubusercontent.com/94850297/195129949-d4f72524-6182-4c4d-8c08-a1ebc42f2c34.png)
![image](https://user-images.githubusercontent.com/94850297/195129985-a9a649ad-8a66-4af8-8e5c-b5618163cc11.png)

The following are steps to construct the transfer learning template pipeline.
1.Load the required package, define the required version through requirements.txt and run it.

![image](https://user-images.githubusercontent.com/94850297/195130028-abce4881-2df4-4286-864a-8c25cb176779.png)

2.The established pipeline diagram, using transfer learning, divides the model into three components.  They are divided into input data, model and output parts. The entire pipeline flow is divided into the parts of establishment, model input layer, and data training.
3.The pipeline establishment process is to extract the training data first, and use two parameters (data_path, model_file) to load the dataset required by the model which are the directory where the original data is located, and the file name of the model respectively.  We use data_path to read the files needed.  It is revealed through built-in jupyter editor of Kubeflow.  All data can be run locally, and it is also convenient to find the data path for training when the model is loaded in later use.

![image](https://user-images.githubusercontent.com/94850297/195130065-94408061-81c1-4ce5-be3b-1297d4ec83f2.png)


4.Then enter the data training stage, use the migration learning of CNN to process the data, first establish a preliminary CNN model, set the top input layer (include_top) to false (remove the input layer to fit into our input part), this Indicates that the VGG16 model will be loaded, excluding the convolutional layers added to the last 3 layers in order to obtain Features.  After the model is built, iterate 10 times to process the input data.

![image](https://user-images.githubusercontent.com/94850297/195130105-8018fb86-1fe8-460e-a34d-ef3f4ac24bcb.png)

5.Go to predict components and load my original data path (data_path) and model (model_file) to make predictions. 

![image](https://user-images.githubusercontent.com/94850297/195130693-4625cb12-801e-4466-a62e-1f6f2c184bdf.png)

6.Next start to build the workflow.  load the components of train and predict into a container, use the function to convert it into a container to package it to build the data. Here, the parameters are the components of train and predict.  The formed image is then used to run all future operations.

![image](https://user-images.githubusercontent.com/94850297/195130746-63277f6d-bddf-41d9-bff4-10e1eaaebc16.png)


7.This step package the image and configure the running status of the pipeline. The sequence is the input of the training model -> model -> training result.  I configure the generated image to /mnt/ of the built-in jupyter, so the path to the pipeline is also required.  The specified data_path is in /mnt/ in order to bring out the trained data, the model trained file is also generated in this path (tf_model.h5), and the predicted image name (0.jpg), in order to establish a working pipeline . Finally, compile the pipeline, package it into yaml and execute it.

![image](https://user-images.githubusercontent.com/94850297/195130777-16e3ec48-bb36-4cb4-9704-9e435eba01bf.png)
![image](https://user-images.githubusercontent.com/94850297/195130821-99c0f862-98c9-4fdb-baf5-671cd3ac4498.png)

8.The complete cycle is a set of pipelines with migration learning as a template. The model components are used to wrap the input layer of the model, and the model can also be replaced with a model based on keras (ex. Caffe, PyTorch, TensorFlow) for training.  The purpose is to apply the data to the trained model, set the image path and the path to generate the mirror image, and realize the deployment of various machine learning on the pipeline.

![image](https://user-images.githubusercontent.com/94850297/195130846-046af3f5-3a50-4011-92e3-4c4c0bd6a594.png)


9.The generated yaml file is used to package the model and the training template. Our model has been compiled into an image and packaged.  The generated file is uploaded to the pipeline for execution.

![image](https://user-images.githubusercontent.com/94850297/195130872-33ab7186-8648-4816-9341-022f049bb440.png)
![image](https://user-images.githubusercontent.com/94850297/195130930-a94fdfb9-3a6b-4cd6-b7c2-89bf644abc33.png)

10. The output data is presented in the pod in the form of training, and 10 batches are repeated to process the images, and then the trained information is stored in tf_model.h5.

![image](https://user-images.githubusercontent.com/94850297/195130941-5f6a5a75-80e1-40e3-9071-ee38086ad525.png)

11. Create a yaml file with pipeline components and output it to kubeflow for k8s as a container orchestration subordinate, and use minikube CLI to view pod logs on the local side.

![image](https://user-images.githubusercontent.com/94850297/195130998-340139fb-5e8a-4f16-96e2-438426f731c6.png)
