--------

Amazon Lookout for Vision is in preview release and is subject to change\.

--------

# Viewing performance metrics<a name="performance-metrics"></a>

You can get performance metrics from the console and by calling the `DescribeModel` operation\. After training is complete, the console displays the performance metrics\. The following procedure shows how to get the performance metrics from a project's model list view\.

The Amazon Lookout for Vision console shows the following performance metrics:
+ [Precision](improve.md#precision-metric)
+ [Recall](improve.md#recall-metric)
+ [F1 score](improve.md#f1-metric)

The test results overview section shows you the total correct and incorrect predictions for images in the test dataset\. You can also see the predicted and actual label assignments for individual images in the test dataset\.

**Topics**
+ [Viewing performance metrics \(console\)](#performance-metrics-console)
+ [Viewing performance metrics \(SDK\)](#performance-metrics-sdk)

## Viewing performance metrics \(console\)<a name="performance-metrics-console"></a>

**To view performance metrics \(console\)**

1. Open the Amazon Lookout for Vision console at [ https://console\.aws\.amazon\.com/lookoutvision/]( https://console.aws.amazon.com/lookoutvision/)\.

1. Choose **Get started**\. 

1. In the left navigation pane, choose **Projects**\.

1. In the projects view, choose the project that contains the version of the model that you want to view\.

1. In the left navigation pane, under the project name, choose **Models**\.

1. In the models list view, choose the versions of the model that you want to view\. 

1. On the model details page, view the performance metrics on the **Performance metrics** tab\.

## Viewing performance metrics \(SDK\)<a name="performance-metrics-sdk"></a>

You can use the [DescribeModel](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_DescribeModel) operation to get the summary performance metrics, the evaluation manifest, and the evaluation results for a model\. 

### Getting the summary performance metrics<a name="performance-metrics-summary-sdk"></a>

The summary performance metrics \([Precision](improve.md#precision-metric), [Recall](improve.md#recall-metric), and [F1 score](improve.md#f1-metric)\) are returned in the `Performance` field returned by a call to `DescribeModel`\.

```
        "Performance": {
            "F1Score": 0.8,
            "Recall": 0.8,
            "Precision": 0.9
        },
```

For information about summary performance metrics see [Step 1: Evaluate the performance of your model](improve.md#evaluate-model)\. For example code, see [Viewing your models \(SDK\)](view-models.md#view-models-sdk)\.

### Using the evaluation manifest<a name="evaluation-manifest"></a>

The evaluation manifest provides test prediction metrics for the individual images used to test a model\. For each image in the test dataset, a JSON line contains the original test \(ground truth\) information and the model's prediction for the image\. Amazon Lookout for Vision stores the evaluation manifest in an Amazon S3 bucket\. You can get the location from the `EvaluationManifest` field in the response from `DescribeModel` operation\.

```
"EvaluationManifest": {
            "Bucket": "lookoutvision-us-east-1-nnnnnnnnnn",
            "Key": "my-sdk-project-model-output/EvaluationManifest-my-sdk-project-1.json"
        }
```

The file name format is `EvaluationManifest-project name.json`\. For example code, see [Viewing your models](view-models.md)\.

In the following sample JSON line, the `class-name` is the ground truth for the contents of the image\. In this example the image contains an anomaly\. The `confidence` field shows the confidence that Amazon Lookout for Vision has in the prediction\. 

```
{
    "source-ref"*: "s3://customerbucket/path/to/image.jpg",
    "source-ref-metadata": {
        "creation-date": "2020-05-22T21:33:37.201882"
    },
   
    // Test dataset ground truth
    "anomaly-label": 1, 
    "anomaly-label-metadata": {
        "class-name": "anomaly",
        "type": "groundtruth/image-classification",
        "human-annotated": "yes",
        "creation-date": "2020-05-22T21:33:37.201882",
        "job-name": "labeling-job/anomaly-detection"
    },
    // Anomaly label detected by Lookout for Vision
    "anomaly-label-detected": 1,
    "anomaly-label-detected-metadata": {
        "class-name": "anomaly",
        "confidence": 0.9,
        "type": "groundtruth/image-classification",
        "human-annotated": "no",
        "creation-date": "2020-05-22T21:33:37.201882",
        "job-name": "training-job/anomaly-detection",
        "model-arn": "lookoutvision-some-model-arn",
        "project-name": "lookoutvision-some-project-name",
        "model-version": "lookoutvision-some-model-version"
    }
}
```

### Reviewing the evaluation result<a name="evaluation-result"></a>

The evaluation result has the following aggregate performance metrics for the entire set of test images: 
+ [Precision](improve.md#precision-metric)
+ [Recall](improve.md#recall-metric)
+ ROC curve \(not shown in console\)
+ Average precision \(not shown in console\)

The evaluation result also includes the number of images used for training and testing the model\.

 Amazon Lookout for Vision stores the evaluation result in an Amazon S3 bucket\. You can get the location by checking the value of `EvaluationResult` in the response from `DescribeModel` operation\.

```
"EvaluationResult": {
            "Bucket": "lookoutvision-us-east-1-nnnnnnnnnn",
            "Key": "my-sdk-project-model-output/EvaluationResult-my-sdk-project-1.json"
        }
```

The file name format is `EvaluationResult-project name.json`\. For an example, see [Viewing your models](view-models.md)\. 

The following schema shows the evaluation result\.

```
{
    "Version": 1,
    "EvaluationDetails": {
        "ModelArn": "string", // The Amazon Resource Name (ARN) of the model version.
        "EvaluationEndTimestamp": "string",  // The date and time that evaluation finished.
        "NumberOfTrainingImages": "int",     // The number of images that were successfully used for training.
        "NumberOfTestingImages": "int",      // The number of images that were successfully used for testing.
    },
    "AggregatedEvaluationResults": {
        "Metrics": {
            "ROCAUC": "float",               // ROC area under the curve.
            "AveragePrecision": "float",     // The average precision of the model.
            "Precision": "float",            // The overall precision of the model.
            "Recall": "float",               // The overall recall of the model.
        }
    }
    
}
```