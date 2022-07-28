# Creating a manifest file<a name="manifest-files"></a>

You can create a dataset by importing an SageMaker Ground Truth format manifest file\. If your images are labeled in a format that isn't a SageMaker Ground Truth manifest file, use the following information to create an SageMaker Ground Truth format manifest file\. 

Manifest files are in [JSON lines](http://jsonlines.org) format where each line is a complete JSON object representing the labeling information for an image\. There are different formats for image [classification](manifest-file-classification.md) and image [segmentation](manifest-file-segmentation.md)\.  Manifest files must be encoded using UTF\-8 encoding\.

**Note**  
The JSON line examples in this section are formatted for readability\. 

The images referenced by a manifest file must be located in the same Amazon S3 bucket\. The manifest file can be in a different bucket\. You specify the location of an image in the `source-ref` field of a JSON line\. 

You can create a manifest file by using code\. The [Amazon Lookout for Vision Lab](https://github.com/aws-samples/amazon-lookout-for-vision/blob/main/Amazon%20Lookout%20for%20Vision%20Lab.ipynb) Python Notebook shows how to create an image classification manifest file for the circuitboard example images\. Alternatively, you can use the [Datasets example code](https://github.com/awsdocs/aws-doc-sdk-examples/blob/master/python/example_code/lookoutvision/datasets.py) in the AWS Code Examples Repository\. You can easily create a manifest file by using a Comma Separated Values \(CSV\) file\. For more information, see [Creating a classification manifest file from a CSV file](ex-csv-manifest.md)\. 

**Topics**
+ [Defining JSON lines for image classification](manifest-file-classification.md)
+ [Defining JSON lines for image segmentation](manifest-file-segmentation.md)
+ [Creating a classification manifest file from a CSV file](ex-csv-manifest.md)