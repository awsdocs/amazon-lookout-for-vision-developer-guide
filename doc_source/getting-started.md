# Getting Started with the Amazon Lookout for Vision console<a name="getting-started"></a>

The Getting Started instructions show you how to create, train, evaluate, and use a model that detects anomalies in images\. To help you learn, Amazon Lookout for Vision provides example images of circuit boards \(that is, `circuitboard`\) that you can use\. 

The general workflow is as follows: 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/lookout-for-vision/latest/developer-guide/images/lfv.png)

You can also use a Lookout for Vision model on an AWS IoT Greengrass Version 2 core device\. For more information, see [Using your Amazon Lookout for Vision model on an edge device](models-devices.md)\.

**Topics**
+ [Set up Amazon Lookout for Vision](#gs-example-images)
+ [Create a project](#gs-project)
+ [Create a dataset](#gs-dataset)
+ [Train your model](#gs-general-train-model)
+ [Evaluate your model](#gs-evaluate-model)
+ [Use your model](#gs-use-model)
+ [Use your dashboard](#gs-dashboard)

## Set up Amazon Lookout for Vision<a name="gs-example-images"></a>

Set up Amazon Lookout for Vision and prepare the *circuitboard* example images for use in Getting Started\. 

**Step 1**: [Set up Amazon Lookout for Vision](su-set-up.md)\. 

Be sure to do [Step 8: \(Optional\) Prepare example images](su-prepare-example-images.md)\.

## Create a project<a name="gs-project"></a>

Create a project to manage the datasets and the models that you create\. A project should be used for a single use case, such as detecting anomalies in a single type of machine part\.  

You can use the dashboard to get an overview of your projects\. For more information, see [Using the Amazon Lookout for Vision dashboard](dashboard.md)\.

**Step 2**: [Create your project](model-create-project.md)\.

## Create a dataset<a name="gs-dataset"></a>

A dataset is a set of images and labels \(that is, normal or anomaly\) that describe those images\. You use datasets to train and test the models you create\. The images should represent a single type of object\. For more information, see [Preparing images for a dataset](model-prepare-images.md)\. With Amazon Lookout for Vision you can have a project that uses a single dataset, or a project that has separate training and test datasets\. We recommend using a single dataset project unless you want finer control over training, testing, and performance tuning\. To create a dataset, you import the images in one of the following ways:
+ Import images from your local computer\.
+ Import images from an S3 bucket\. Amazon Lookout for Vision can label the images using the folder names that contain the images\.
+ Import an Amazon SageMaker Ground Truth manifest file\.

To help you understand how Amazon Lookout for Vision works, create a project with a training dataset and a test dataset\. Import your training dataset images from *s3://*your bucket*/circuitboard/train/*\. Import your test dataset images from *s3://*your bucket*/circuitboard/test/*\. 

**Step 3**: [Create your dataset \(Import images from Amazon S3\)](create-dataset-s3.md)\.

After you create the dataset, you can add more images and also label the images\. For more information, see [Editing your dataset](edit-dataset.md)\. For Getting Started, you don't need to add any more images to create your first model\.

## Train your model<a name="gs-general-train-model"></a>

Training creates a model and trains it to predict the presence of anomalies in images\. A new version of your model is created each time you train\.

At the start of training, Amazon Lookout for Vision chooses the most suitable algorithm to train your model with\. The model is trained and then tested\. When you train a single dataset project, the dataset is internally split to create a training dataset and a test dataset\. In Getting Started, your project has separate training and test datasets\. In this configuration, Amazon Lookout for Vision trains your model with the training dataset and tests the model with the test dataset\. 

**Step 4**: [Train your model](model-train.md)\.

**Note**  
You are charged for the amount of time it takes to successfully train your model\.

## Evaluate your model<a name="gs-evaluate-model"></a>

Evaluate the performance of your model by using the performance metrics created during testing\. 

Performance metrics enable you to understand the performance of your trained model, and decide if you're ready to use it in production\.

**Step 5**: [View your performance metrics](performance-metrics.md)\.

**Step 6**: [Evaluate and improve your model](improve.md)\.

If the performance metrics indicate that improvements are needed, you can add more training data by running a trial detection task with new images\. After the task completes you can verify the results and add the verified images to your training dataset\. Alternatively, you can add new training images directly to the dataset\. In these steps, improve the model by running a trial detection task and adding the verified images to your training dataset\. Use the example images in the `extra_images` folder\.

**Step 7**: [Run a trial detection task](trial-detection.md)\.

**Step 8**: [Retrain your model](model-train.md)\.

If the performance results indicate acceptable performance, you can verify the performance by running a trial detection\. The performance results after retraining should confirm the readiness of your model\. 

## Use your model<a name="gs-use-model"></a>

Before you can use your model, you start the model with the [StartModel](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_StartModel) operation\. You can get the `StartModel` CLI command for your model from the console\.

**Step 9**: [Start your model](run-start-model.md)\.

A trained Amazon Lookout for Vision model predicts whether an input image contains normal or anomalous content\.

To make a prediction with your model, call the [DetectAnomalies](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_DetectAnomalies) operation and pass an input image from your local computer\. For Getting Started, use one of the images in the `extra_images` folder\. You can get the CLI command that calls `DetectAnomalies` from the console\.

**Step 10**: [Detect anomalies in an image](inference-detect-anomalies.md)\.

You are charged for the time that your model is running\. If you are no longer using your model, use the [StopModel](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_StopModel) operation to stop the model\. You can get the CLI command from the console\.

**Step 11**: [Stop your model](run-stop-model.md)\.

## Use your dashboard<a name="gs-dashboard"></a>

You can use the dashboard to get an overview of all your projects and overview information for individual projects\. 

**Step 12**: [Use your dashboard](dashboard.md)\.