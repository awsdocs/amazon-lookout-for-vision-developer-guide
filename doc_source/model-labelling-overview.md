# Labeling images<a name="model-labelling-overview"></a>

You can use the Amazon Lookout for Vision console to add or modify the labels assigned to images in your dataset\. If you're using the SDK, the labels are part of the manifest file you supply to [CreateDataset](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_CreateDataset)\. You can update the labels for an image by calling [UpdateDatasetEntries](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_UpdateDatasetEntries)\. For example code, see [Adding more images \(SDK\)](edit-dataset.md#add-more-images-dataset-sdk)\. 

## Choosing the model type<a name="model-labeling-model-type"></a>

The labels you assign to images determine the [type](understanding.md#ud-choose-model-type) of model that Lookout for Vision creates\. If your project has a separate test dataset, label the images in the same way\. 

### Image classification model<a name="model-labeling-model-type-classification"></a>

To create an image classification model, you classify each image as normal or anomalous\. For each image, do [Classifying images \(console\)](model-label.md)\.

### Image segmentation model<a name="model-labeling-model-type-segmentation"></a>

To create an image segmentation model, you classify each image as normal or anomalous\. For each anomalous image, you also specify a pixel mask for each anomalous area on the image and an anomaly label for the type of anomaly within the pixel mask\. For example, the blue mask in the following image marks the location of a scratch anomaly type on a car\. You can specify more than one type of anomaly label in an image\. For each image, do [Segmenting images \(console\)](segment-image.md)\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/lookout-for-vision/latest/developer-guide/images/mask.png)