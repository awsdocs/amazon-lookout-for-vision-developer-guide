--------

Amazon Lookout for Vision is in preview release and is subject to change\.

--------

# Detecting anomalies in an image<a name="inference-detect-anomalies"></a>

To detect anomalies in an image with a trained Amazon Lookout for Vision model, you call the [DetectAnomalies](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_DetectAnomalies) operation\. The result from `DetectAnomalies` includes a boolean prediction that the image contains one or more anomalies and a confidence value for the prediction\. 

Images analyzed with `DetectAnomalies` must have the same dimensions as the images used to train the model\.

**Note**  
Before calling `DetectAnomalies`, you must first start your model with the `StartModel` operation\. For more information, see [Starting your Amazon Lookout for Vision model](run-start-model.md)\. You are charged for the amount of time, in minutes, that a model runs and for the number of anomaly detection units that your model uses\. If you are not using a model, use the `StopModel` operation to stop your model\. For more information, see [Stopping your Amazon Lookout for Vision model](run-stop-model.md)\.

To call `DetectAnomalies`, specify the following:
+ **Project** – The name of the project that contains the model that you want to use\.
+ **ModelVersion** – The version of the model that you want to use\.
+ **ContentType** – The type of image that you want analyze\. Valid values are `image/png` \(PNG format images\) and `image/jpeg` \(JPG format images\)\. 
+ **Body** – The unencoded binary bytes that represent the image\.

The response from `DetectAnomalies` contains the following: 
+ **IsAnomalous**– A boolean indicator that the image contains one or more anomalies\.
+ **Confidence**– The confidence that Amazon Lookout for Vision has in the accuracy of the anomaly prediction \(`IsAnomalous`\)\. `Confidence` is a floating point value between 0 and 1\. A higher value indicates a higher confidence\.
+ **Source** – Information about the image passed to `DetectAnomalies`\.

```
{
    "DetectAnomalyResult": {
        "Source": {
            "Type": "direct"
        },
        "IsAnomalous": true,
        "Confidence": 0.9996867775917053
    }
}
```

If you're finding the confidence values returned by `DetectAnomalies` are too low, consider retraining the model\.  

**To detect anomalies in an image \(API\)**

1. If you haven't already done so, do the following:

   1. Create or update an IAM user with `AmazonLookoutVisionFullAccess` For more information, see [Step 2: Create an IAM administrator user and group](su-account-user.md)\.

   1. Install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 4: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

   1. [Train your model](model.md)\.

   1. [Start your model](run-start-model.md)\.

1. Ensure the IAM user calling `DetectAnomalies` has access to the model version that you want to use\. 

1. Use the following examples to call the `DetectAnomalies` operation\.
**Tip**  
If you're using the Getting Started images, use an image from the `extra_images` folder\.

------
#### [ AWS CLI ]

   This AWS CLI command displays the JSON output for the `DetectAnomalies` CLI operation\. Change the values of the following input parameters: 
   + `project name` with the name of the project that you want to use\. 
   + `model version` with the version of the model that you want to use\.
   + `content type` with the type of the image that you want to use\. Valid values are `image/png` \(PNG format images\) and `image/jpeg` \(JPG format images\)\. 
   + `file name` with the path and file name of the image that you want to use\. Ensure the file type matches the value of `content-type`\. 

   ```
   aws lookoutvision detect-anomalies  --project-name project name\
       --model-version model version\
       --content-type content type\
       --body file name
   ```

------
#### [ Python ]

   The following example code detects anomalies in an image that you supply\. Replace the following values: 
   + `project_name` with the name of the project that contains the model version that you want to use\. 
   + `model_version` with the version of the model that you want to use\. 
   + `photo` with the name and file path of a JPG format image file that you want to use\.
   + \(Optional\) If you want to pass a PNG format file, change the `detect_anomalies` input parameter `ContentType='image/jpeg'` to `ContentType='image/png'`\. 

   ```
   #Copyright 2021 Amazon.com, Inc. or its affiliates. All Rights Reserved.
   
   import boto3
   
   def detect_anomalies(project_name,model_version,photo):
        
   
       client=boto3.client('lookoutvision')
   
       #Call DetectAnomalies 
   
       with open(photo, 'rb') as image:
           response = client.detect_anomalies(ProjectName=project_name, 
           ContentType='image/jpeg',
           Body=image.read(),
           ModelVersion=model_version)
       print ('Anomalous?: ' + str(response['DetectAnomalyResult']['IsAnomalous']))
       print ('Confidence: ' + str(response['DetectAnomalyResult']['Confidence']))
   
   
   def main():
   
       project_name='my-project'
       photo='image.jpg'
       model_version='1'
     
       anomalous=detect_anomalies(project_name,model_version,photo)
       
   
   if __name__ == "__main__":
       main().
   ```

------

1. If you aren't planning to continue using your model, [stop your model](run-stop-model.md)\. 