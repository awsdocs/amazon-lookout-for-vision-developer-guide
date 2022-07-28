# Segmenting images \(console\)<a name="segment-image"></a>

If you're creating an image segmentation model, you must classify images as normal or anomaly\. You must also add segmentation information to anomalous images\. To specify segmentation information, you first specify the anomaly types, such as a dent, that you want your model to find\. Then you specify an anomaly mask and anomaly type for each anomaly on anomalous images in your dataset\.

**Note**  
If you're creating an image classification model, you don't need to segment images and you don't need to specify anomaly types\.

**Topics**
+ [Specifying anomaly types](#segment-image-anomaly-types)
+ [Labeling an image](#segment-image-label)
+ [Segmenting an image with the annotation tool](#segment-image-annotation-tool)

## Specifying anomaly types<a name="segment-image-anomaly-types"></a>

You define an anomaly type for each type of anomaly that's in the dataset images\.

**Specify anomaly types**

1. In the left navigation pane of your project, choose **Dataset**\.

1. In **Anomaly types** choose **Add anomaly types**\. If you've previously added an anomaly type, choose **Manage**\.

1. In the dialog box, do the following: 

   1. Enter the name for the anomaly type that you want to add and choose **Add anomaly type**\.

   1. Repeat the previous step until you have entered every anomaly type that you want your model to find\.

   1. \(Optional\) Choose the edit icon to change the name of an anomaly type\.

   1. \(Optional\) Choose the delete icon to delete a new anomaly type\. You can't delete anomaly types that your dataset is currently using using\. 

1. Choose **Confirm** to add the new anomaly types to the dataset\.

After you specify the anomaly types, label the images by doing [Labeling an image](#segment-image-label)\.

## Labeling an image<a name="segment-image-label"></a>

To label an image for image segmentation, classify the image as normal or an anomaly\. Then, use the annotation tool to segment the image by drawing masks that tightly cover the anomalous areas for each type of anomaly present in the image\.

**To label an image**

1.  If you have separate training and test datasets, choose the tab for the dataset that you want to use\.

1. If you haven't already, specify the anomaly types for your dataset by doing [Specifying anomaly types](#segment-image-anomaly-types)\.

1. Choose **Add labels**\.

1. Choose **Select all images on this page**\.

1. If the images are normal, choose **Classify as normal**, otherwise choose **Classify as anomaly**\. 

1. If you need to change the label for an image, do the following:

   1. Choose **Normal** or **Anomaly** under the image\.

   1. If you can't determine the correct label for an image, magnify the image by choosing the image in the gallery\.
**Note**  
You can filter image labels by choosing the desired label, or label state, in the **Filters** section\. You can sort by confidence score in the **Sorting options** section\.

1. For each anomalous image, choose **Anomalous area** under the image to open the annotation tool\. Add segmentation information by doing [Segmenting an image with the annotation tool](#segment-image-annotation-tool)\.

1. Choose **Save changes**\.

1. If you've finished labeling your images, you can [train](model-train.md) your model\.

## Segmenting an image with the annotation tool<a name="segment-image-annotation-tool"></a>

You use the annotation tool to segment an image by marking anomalous areas with a mask\.

**To segment an image with the annotation tool**

1. Open the annotation tool by selecting **anomalous area** under an image in the dataset gallery\.

1. In the **Anomaly types** section choose the anomaly type that you want to mark\. If necessary, choose **Add anomaly type** to add a new anomaly type\.

1. Choose a drawing tool at the bottom of the page and draw masks that tightly covers anomalous areas for the anomaly type\. The following image is an example of a mask that tightly covers an anomaly\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/lookout-for-vision/latest/developer-guide/images/good-mask.png)

   The following is an example of a poor mask that doesn't tightly cover an anomaly\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/lookout-for-vision/latest/developer-guide/images/poor-mask.png)

1. If you have more images to segment, choose **Next** and repeat steps 2 and 3\.

1. Choose **Submit and close** to finish segmenting images\.