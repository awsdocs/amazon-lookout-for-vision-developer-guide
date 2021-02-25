# Getting started with the AWS SDK<a name="getting-started-sdk"></a>

The Getting Started instructions show you how to use the AWS SDK to create, train, evaluate, and use a model that detects anomalies in an image\. We recommend reading [Getting Started with the Amazon Lookout for Vision console](getting-started.md) first\. This section includes AWS CLI commands\. You can get more information and Python examples by choosing the **More information** link at the end of each section\.

Also, the [Amazon Lookout for Vision Lab](https://github.com/aws-samples/amazon-lookout-for-vision/blob/main/Amazon%20Lookout%20for%20Vision%20Lab.ipynb) GitHub repository includes a Python Notebook that you can use to create a model with the circuitboard example images\. 

To prepare, train, evaluate, improve, and use a model, do the following:

**Topics**
+ [Set up Amazon Lookout for Vision](#gs-setup-sdk)
+ [Create a project](#gs-project)
+ [Upload your images](#gs-upload-images-sdk)
+ [Create a manifest file](#gs-create-manifest-sdk)
+ [Create a dataset](#gs-dataset-sdk)
+ [Train your model](#gs-general-train-model-sdk)
+ [Evaluate your model](#gs-evaluate-model-sdk)
+ [Use your model](#gs-use-model-sdk)

## Set up Amazon Lookout for Vision<a name="gs-setup-sdk"></a>

Set up Amazon Lookout for Vision and prepare the example images for use in Getting Started\. 

**More information**: [Setup Amazon Lookout for Vision](su-set-up.md)\. 

Be sure to do [Step 5: \(Optional\) Prepare example images](su-prepare-example-images.md)\.

## Create a project<a name="gs-project"></a>

You create a project to manage the datasets and the models that you create\. 

**Note**  
Amazon Lookout for Vision stores your project files in an Amazon S3 bucket created for you when you first open the console\. If you haven't previously used the console, [create the console bucket](su-create-console-bucket.md)\. 

To create a project, call the [CreateProject](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_CreateProject) operation and specify a project name\. The following example creates a project named `my-sdk-project`\.

```
aws lookoutvision create-project --project-name my-sdk-project
```

The JSON response examples use a project named `my-sdk-project`\.

```
{
    "ProjectMetadata": {
        "ProjectArn": "arn:aws:lookoutvision:us-east-1:nnnnnnnnnnn:project/my-sdk-project",
        "ProjectName": "my-sdk-project",
        "CreationTimestamp": 1607277318.389
    }
}
```

**More information**: [Create your project](model-create-project.md#create-project-sdk)\.

## Upload your images<a name="gs-upload-images-sdk"></a>

You store the images used to train a model in an Amazon S3 bucket\. For Getting Started, use the circuitboard images in the [Amazon Lookout for Vision Lab](https://github.com/aws-samples/amazon-lookout-for-vision/blob/main/Amazon%20Lookout%20for%20Vision%20Lab.ipynb) GitHub repository\. If you haven't already, do [Step 5: \(Optional\) Prepare example images](su-prepare-example-images.md)\.

## Create a manifest file<a name="gs-create-manifest-sdk"></a>

To create a dataset with the AWS SDK, you need an Amazon SageMaker Ground Truth format manifest file\. A manifest file provides information about the images used for training\. For example, the S3 bucket location of images and labels that classify images as normal or anomalous\. Each image in the manifest file is represented by a JSON Line, as shown in the following example:

```
{
    "source-ref": "s3://lookoutvision-console-us-east-1-nnnnnnnnnn/gt-job/manifest/IMG_1133.png",
    "anomaly-label": 1,
    "anomaly-label-metadata": {
        "confidence": 0.95,
        "job-name": "labeling-job/testclconsolebucket",
        "class-name": "normal",
        "human-annotated": "yes",
        "creation-date": "2020-04-15T20:17:23.433061",
        "type": "groundtruth/image-classification"
    }
}
```

You can use an existing manifest file, such as the output from an SageMaker Ground Truth job, or you can create your own manifest file\. 

**Tip**  
If you are creating your dataset using the circuitboard example images, you have two options:  
Create the manifest file using code\. The [Amazon Lookout for Vision Lab](https://github.com/aws-samples/amazon-lookout-for-vision/blob/main/Amazon%20Lookout%20for%20Vision%20Lab.ipynb) Python Notebook shows how to create the manifest file for the circuitboard example images\. 
If you've already followed [Getting Started with the Amazon Lookout for Vision console](getting-started.md) and created a dataset with the circuitboard example images, reuse the manifest files created for you by Amazon Lookout for Vision\. The training and test manifest file locations are `s3://bucket/datasets/project name/train or test/manifests/output/output.manifest`\. 

Upload your completed manifest files to a folder within the bucket that contains your images\. 

**More information**: [Create a manifest file](manifest-files.md)\.



## Create a dataset<a name="gs-dataset-sdk"></a>

A dataset is a set of images and labels \(that is, normal or anomaly\) that describe those images\. You use datasets to train and test the models you create\. With Amazon Lookout for Vision you can create a project that uses a single dataset, or create a project that has separate training and test datasets\.  

For Getting Started, create a project with a training dataset and a test dataset\.

To create the training dataset, call [CreateDataset](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_CreateDataset) and specify a dataset type of `train`\. 

```
aws lookoutvision create-dataset --project-name "my-sdk-project"\
  --dataset-type train\
  --dataset-source '{ "GroundTruthManifest": { "S3Object": { "Bucket": "bucket", "Key": "manifest file" } } }'
```

To create the test dataset, call `CreateDataset` and specify a dataset type of `test`\.

```
aws lookoutvision create-dataset --project-name "my-sdk-project"\
  --dataset-type test\
  --dataset-source '{ "GroundTruthManifest": { "S3Object": { "Bucket": "bucket", "Key": "manifest file" } } }'
```

Depending on the number of images you have, it might take a while to create the dataset\. You can use the `DescribeDataset` operation to check the current status\.

```
aws lookoutvision describe-dataset --project-name "my-sdk-project"\
  --dataset-type train or test
```

If the value of `Status` is `CREATE_COMPLETE`, the dataset has been successfully created\. 

```
{
    "DatasetDescription": {
        "ProjectName": "my-sdk-project",
        "DatasetType": "test",
        "CreationTimestamp": 1606579439.278,
        "LastUpdatedTimestamp": 1606579453.6,
        "Status": "CREATE_COMPLETE",
        "StatusMessage": "The dataset was created successfully.",
        "ImageStats": {
            "Total": 44,
            "Labeled": 44,
            "Normal": 34,
            "Anomaly": 10
        }
    }
}
```

**More information**: [Create your dataset](create-dataset-sdk.md)\.

## Train your model<a name="gs-general-train-model-sdk"></a>

At the start of training, Amazon Lookout for Vision chooses the most suitable algorithm to train the model with\. The model is trained and then tested using the test dataset\. A new version of your model is created each time you train it\. 

When you train a single dataset project, the dataset is internally split to create a training dataset and a test dataset\. If your project has separate training and test datasets, they are separately used to train and test the model\. 

To train a model, use the [CreateModel](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_CreateModel) operation\. You provide the project name that contains the dataset that you want to use\. You also specify an output location for the training results\.

```
aws lookoutvision create-model --project-name "my-sdk-project"\
  --output-config '{ "S3Location": { "Bucket": "output bucket", "Prefix":  "output folder" } }'
```

In the response, note the value of `ModelVersion`\. If this is the first time you've trained a model in this project, the value is `1`\.  

```
{
    "ModelMetadata": {
        "CreationTimestamp": 1607278983.132,
        "ModelVersion": "1",
        "ModelArn": "arn:aws:lookoutvision:us-east-1:nnnnnnnnnnn:model/my-sdk-project/1",
        "Status": "TRAINING",
        "StatusMessage": "The model is being trained."
    }
}
```

Training takes a while to complete\. To check the current status, call [DescribeModel](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_DescribeModel) and pass the project name and the model version\. 

```
aws lookoutvision  describe-model --project-name "my-sdk-project"\
    --model-version "model version"
```

To get the current status, check the `Status` field in the response\. Training has successfully completed when the value of `Status` is `TRAINED`\.

```
{
    "ModelDescription": {
        "ModelVersion": "1",
        "ModelArn": "arn:aws:lookoutvision:us-east-1:nnnnnnnnnnnn:model/my-sdk-project/1",
        "CreationTimestamp": 1606581265.586,
        "Status": "TRAINING",
        "StatusMessage": "The model is being trained.",
        "OutputConfig": {
            "S3Location": {
                "Bucket": "lookoutvision-us-east-1-nnnnnnnnnn",
                "Prefix": "my-sdk-project-model-output/"
            }
        }
    }
}
```

**More information**: [Train your model](model-train.md#create-model-sdk)\.

**Note**  
You are charged for the amount of time it takes to successfully train your model\.

## Evaluate your model<a name="gs-evaluate-model-sdk"></a>

Evaluate the performance of your model by using the performance metrics created during testing\. 

Performance metrics enable you to understand the performance of your trained model, and decide if you're ready to use it in production\. For more information, see [Improving your model](improve.md)\.

You can view the performance metrics by calling the `DescribeModel` operation\.

```
aws lookoutvision  describe-model --project-name "my-sdk-project"\
    --model-version "model version"
```

The `Performance` field in the response includes summary precision, recall, and F1 metrics for the model\. `EvaluationManifest` contains the location of the evaluation manifest which includes metrics for individual test images\. For more information, see [Using the evaluation manifest](performance-metrics.md#evaluation-manifest)\. `EvaluationResult` contains the location of overview performance metrics\. For more information, see [Reviewing the evaluation result](performance-metrics.md#evaluation-result)\. 

```
{
    "ModelDescription": {
        "ModelVersion": "1",
        "ModelArn": "arn:aws:lookoutvision:us-east-1:nnnnnnnnnnnn:model/my-sdk-project/1",
        "CreationTimestamp": 1606581265.586,
        "Status": "TRAINED",
        "StatusMessage": "Training completed successfully.",
        "Performance": {
            "F1Score": 1.0,
            "Recall": 1.0,
            "Precision": 1.0
        },
        "OutputConfig": {
            "S3Location": {
                "Bucket": "lookoutvision-us-east-1-nnnnnnnnnn",
                "Prefix": "my-sdk-project-model-output/"
            }
        },
        "EvaluationManifest": {
            "Bucket": "lookoutvision-us-east-1-nnnnnnnnnn",
            "Key": "my-sdk-project-model-output/EvaluationManifest-my-sdk-project-1.json"
        },
        "EvaluationResult": {
            "Bucket": "lookoutvision-us-east-1-nnnnnnnnnn",
            "Key": "my-sdk-project-model-output/EvaluationResult-my-sdk-project-1.json"
        },
        "EvaluationEndTimestamp": 1606583060.681
    }
}
```

**Note**  
You can use the Amazon Lookout for Vision console to create a trial detection and improve your model by adding verified images to your dataset\. You can't use the AWS SDK to create a trial detection\. 

If the performance metrics indicate that improvements are needed, add more labeled training images by calling the [UpdateDatasetEntries](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_UpdateDatasetEntries) operation\. Afterwards, retrain your model\.



****More information:****
+ [View performance metrics](performance-metrics.md#performance-metrics-sdk)\.
+ [Add more images](create-dataset-sdk.md#add-labeled-images-sdk)\.

## Use your model<a name="gs-use-model-sdk"></a>

To detect anomalies in new images you first have to start your model with the [StartModel](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_StartModel) operation\. You pass the project name and the version of the model that you want to start\.

```
aws lookoutvision start-model --project-name "my-sdk-project"\
  --model-version "model version"\
  --min-inference-units 1
```

It might take a few minutes to start your model\. Check the status by calling `DescribeModel`\.

```
aws lookoutvision  describe-model --project-name "my-sdk-project"\
    --model-version "model version"
```

The model is running if the Status field is `HOSTED`\. 

**More information**: [Start your model](run-start-model.md)\.

A trained Amazon Lookout for Vision model predicts if an input image contains normal or anomalous content\.

To make a prediction with your model, call the [DetectAnomalies](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_DetectAnomalies) operation and pass an input image from your local computer\. You also pass the project name and the version of the model that you want to use\. Use a PNG or JPEG format image\.

```
aws lookoutvision detect-anomalies  --project-name "my-sdk-project"\
    --model-version "model version"\
    --content-type "image/png or image/jpeg"\
    --body "image path and file name"
```

**More information**: [Detect anomalies in an image](inference-detect-anomalies.md)\.

The response includes a prediction for the presence of an anomaly and the confidence that Amazon Lookout for Vision has in the accuracy of the prediction\.

```
{
    "DetectAnomalyResult": {
        "Source": {
            "Type": "direct"
        },
        "IsAnomalous": false,
        "Confidence": 0.9999964237213135
    }
}
```

You are charged for the time that your model is running\. If you are no longer using your model, use the [StopModel](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_StopModel) operation to stop the model\.

```
aws lookoutvision stop-model --project-name "my-sdk-project"\
    --model-version "model version"
```

**More information**: [Stop your model](run-stop-model.md#stop-model-sdk)\.