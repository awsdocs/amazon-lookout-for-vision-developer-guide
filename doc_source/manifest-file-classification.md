# Defining JSON lines for anomaly classification<a name="manifest-file-classification"></a>

You define a JSON line for each image that you want to use in an Amazon Lookout for Vision manifest file\. The JSON line determines whether the image contains normal or anomalous content\. A JSON line is in SageMaker Ground Truth [Classification Job Output](https://docs.aws.amazon.com/sagemaker/latest/dg/sms-data-output.html#sms-output-class) format\. A manifest file is made of one or more JSON lines, one for each image that you want to import\. 

**To create a manifest file for image\-level labels**

1. Create an empty text file\.

1. Add a JSON line for each image the that you want to import\. Each JSON line should look similar to the following:

   ```
   {"source-ref":"s3://lookoutvision-console-us-east-1-nnnnnnnnnn/gt-job/manifest/IMG_1133.png","anomaly-label":1,"anomaly-label-metadata":{"confidence":0.95,"job-name":"labeling-job/testclconsolebucket","class-name":"normal","human-annotated":"yes","creation-date":"2020-04-15T20:17:23.433061","type":"groundtruth/image-classification"}}
   ```

1. Save the file\. 
**Note**  
You can use the extension `.manifest`, but it is not required\. 

1. Create a dataset using the manifest file that you created\. For more information, see [Creating a manifest file](manifest-files.md)\. 

 

## Image\-level JSON lines<a name="cd-manifest-classification-json"></a>

In this section, you learn how to create a JSON line for an image that is either normal or anomalous\.

### Anomaly JSON line<a name="classification-anomaly"></a>

The following JSON line shows an image that is labeled as an anomaly\. Note that the value of `class-name` is `anomaly`\. 

```
{
    "source-ref": "s3: //bucket/image/anomaly/abnormal-1.jpg",
    "anomaly-label-metadata": {
        "confidence": 1,
        "job-name": "labeling-job/auto-label",
        "class-name": "anomaly",
        "human-annotated": "yes",
        "creation-date": "2020-11-10T03:37:09.600",
        "type": "groundtruth/image-classification"
    },
    "anomaly-label": 1
}
```

### Normal JSON line<a name="classification-normal"></a>

The following JSON line shows an image labeled as normal\. Note that the value of `class-name` is `normal`\.

```
{
    "source-ref": "s3: //bucket/image/normal/2020-10-20_12-14-55_613.jpeg",
    "anomaly-label-metadata": {
        "confidence": 1,
        "job-name": "labeling-job/auto-label",
        "class-name": "normal",
        "human-annotated": "yes",
        "creation-date": "2020-11-10T03:37:09.603",
        "type": "groundtruth/image-classification"
    },
    "anomaly-label": 0
}
```

## JSON line keys and values<a name="json-line-format"></a>

The following information describes the keys and values in an Amazon Lookout for Vision JSON line\.

### source\-ref<a name="w143aac22c13c25b7c17c11b5"></a>

\(Required\) The Amazon S3 location of the image\. The format is `"s3://BUCKET/OBJECT_PATH"`\. Images in an imported dataset must be stored in the same Amazon S3 bucket\. 

### anomaly\-label<a name="w143aac22c13c25b7c17c11b7"></a>

\(Required\) The label attribute\. Use the key `anomaly-label`, or another key name that you choose\. The key value \(`0` in the preceding example\) is required by Amazon Lookout for Vision, but it isn't used\. The output manifest created by Amazon Lookout for Vision converts the value to `0` for an anomalous image and a value of `1` for a normal image\. The value of `class-name` determines if the image is normal or anomalous\. 

There must be corresponding metadata identified by the field name with *\-metadata* appended\. For example, `"anomaly-label-metadata"`\. 

### anomaly\-label\-metadata<a name="w143aac22c13c25b7c17c11b9"></a>

\(Required\) Metadata about the label attribute\. The field name must be the same as the label attribute with *\-metadata* appended\. 

*confidence*  
\(Optional\) Currently not used by Amazon Lookout for Vision\. If you do specify a value, use a value of `1`\. 

*job\-name*  
\(Optional\) A name that you choose for the job that processes the image\. 

*class\-name*  
\(Required\) If the image contains normal content, specify `normal`, otherwise specify `anomaly`\. If the value of `class-name` is any other value, the image is added to the dataset as an unlabeled image\. To label an image, see [Editing your dataset](edit-dataset.md)\. 

*human\-annotated*  
\(Required\) Specify `"yes"`, if the annotation was completed by a human\. Otherwise, specify `"no"`\. 

*creation\-date*   
\(Optional\) The Coordinated Universal Time \(UTC\) date and time that the label was created\. 

*type*  
\(Required\) The type of processing that should be applied to the image\. For image\-level anomaly labels, the value is `"groundtruth/image-classification"`\. 