# Verifying your model with a trial detection task<a name="trial-detection"></a>

If you want to verify or improve the quality of your model, you can run a trial detection task\. A trial detection task detects anomalies in new images that you supply\. 

You can verify the detection results and add the verified images to your dataset\. If you have separate training and test datasets, the verified images are added to the training dataset\.

You can verify images from your local computer or images located in an Amazon S3 bucket\. If you want to add verified images to the dataset, images located in an S3 bucket must be in the same S3 bucket as the images in your dataset\.

**Note**  
To run a trial detection task, ensure that your S3 bucket has versioning enabled\. For more information, see [Using versioning](https://docs.aws.amazon.com/AmazonS3/latest/dev/Versioning.html)\. The console bucket is created with versioning enabled\.

By default your images are encrypted with a key that AWS owns and manages\. You can also choose to use your own AWS Key Management Service \(KMS\) key\. For more information, see [AWS Key Management Service concepts](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#master_keys)\.

**Topics**
+ [Running a trial detection task](#run-trial-detection)
+ [Verifying trial detection results](#verify-trial-detection-results)
+ [Correcting segmentation labels with the annotation tool](#verify-trial-detection-results-annotation-tool)

## Running a trial detection task<a name="run-trial-detection"></a>

Perform the following steps to run a trial detection task\.

**To run a trial detection \(console\)**

1. Open the Amazon Lookout for Vision console at [ https://console\.aws\.amazon\.com/lookoutvision/]( https://console.aws.amazon.com/lookoutvision/)\.

1. Choose **Get started**\. 

1. In the left navigation pane, choose **Projects**\.

1. In the projects view, choose the project that contains the version of the model that you want to view\.

1. In the left navigation pane, under the project name, choose **Trial detections**\. 

1. In the trial detections view, choose the **Run trial detection**\. 

1. On the **Run trial detection** page, enter a name for your trial detection task in **Task name**\.

1. In **Choose model**, choose the version of that model that you want to use\.

1. Import the images according to the source of the images as follows:
   + If you are importing your source images from an Amazon S3 bucket, enter the **S3 URI**\.
**Tip**  
If you're using the Getting Started example images, use the *extra\_images* folder\. The Amazon S3 URI is `s3://your bucket/circuitboard/extra_images`\.
   + If you are uploading images from your computer, add the images after you choose **Detect anomalies**\. 

1. \(Optional\) If you want to use your own AWS KMS encryption key, do the following:

   1. For **Image data encryption**, choose **Customize encryption settings \(advanced\)**\.

   1. In **encryption\.aws\_kms\_key**, enter the Amazon Resource Name \(ARN\) of your key, or choose an existing AWS KMS key\. To create a new key, choose **Create an AWS IMS key**\.

1. Choose **Detect anomalies** and then choose **Run trial detection** to start the trial detection task\.

1. Check the current status in the trial detections view\. The trial detection might take a while to complete\. 

## Verifying trial detection results<a name="verify-trial-detection-results"></a>

Verifying the results of a trial detection can help you improve your model\.

If the performance metrics are poor, improve your model by running a trial detection and then add verified images to the dataset \(training dataset, if you have a separate datasets\)\.

If the model's performance metrics are good, but the results of a trial detection are poor, you can improve your model by adding verified images to the dataset \(training dataset\)\. If you have a separate test dataset, consider adding more images to the test dataset\. 

After you add verified images to your dataset, retrain and re\-evaluate your model\. For more information, see [Training your model](model-train.md)\. 

**To verify the results of a trial detection**

1. Open the Amazon Lookout for Vision console at [ https://console\.aws\.amazon\.com/lookoutvision/]( https://console.aws.amazon.com/lookoutvision/)\.

1. In the left navigation pane, choose **Projects**\.

1. In the **Projects** page, choose the project that you want to use\. The dashboard for your project is displayed\.

1. In the left navigation pane, choose **Trial detections**\.

1. Choose the trial detection that you want to verify\. 

1. On the trial detection page, choose **Verify machine predictions**\.

1. Choose **Select all images on this page**\.

1. If the predictions are correct, choose **Verify as correct**\. Otherwise, choose **Verify as incorrect**\. The prediction and prediction confidence score is shown under each image\.

1. If you need to change the label for an image, do the following:

   1. Choose **Correct** or **Incorrect** under the image\.

   1. If you can't determine the correct label for an image, magnify the image by choosing the image in the gallery\.
**Note**  
You can filter image labels by choosing the desired label, or label state, in the **Filters** section\. You can sort by confidence score in the **Sorting options** section\.

1. If your model is a segmentation model and the mask or anomaly type for an image is wrong, choose **Anomalous area** under the image and open the annotation tool\. Update the segmentation information by doing [Correcting segmentation labels with the annotation tool](#verify-trial-detection-results-annotation-tool)\.

1. Repeat steps 7\-10 on each page as necessary until all the images have been verified\.

1. Choose **Add verified images to dataset**\. If you have separate datasets, the images are added to the training dataset\.

1. Retrain your model\. For more information, see [Training your model](model-train.md)\.

## Correcting segmentation labels with the annotation tool<a name="verify-trial-detection-results-annotation-tool"></a>

You use the annotation tool to segment an image by marking anomalous areas with a mask\.

**To correct the segmentation labels for an image with the annotation tool**

1. Open the annotation tool by selecting **anomalous area** under an image in the dataset gallery\.

1. If the anomaly type for a mask isn't correct, choose the mask and then choose the correct anomaly type under **Anomaly types**\. If necessary, choose **Add anomaly type** to add a new anomaly type\.

1. If the mask isn't correct, choose a drawing tool at the bottom of the page and draw masks that tightly covers anomalous areas for the anomaly type\. The following image is an example of a mask that tightly covers an anomaly\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/lookout-for-vision/latest/developer-guide/images/good-mask.png)

   The following is an example of a poor mask that doesn't tightly cover an anomaly\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/lookout-for-vision/latest/developer-guide/images/poor-mask.png)

1. If you have more images to correct, choose **Next** and repeat steps 2 and 3\.

1. Choose **Submit and close** to finish updating images\.