# Troubleshooting model training<a name="troubleshoot-model-training"></a>

Issues with your manifest file or training images can cause model training to fail\. Before retraining your model, check the following potential issues\. 

## Anomaly label colors don't match color of anomalies in mask image<a name="troublehooting-color-matching"></a>

If you are training an image segmentation model, the color of the anomaly label in the manifest file must match the color that's in the mask image\. The JSON line for an image in the manifest file has metadata \(`internal-color-map`\) that tells Amazon Lookout for Vision which color corresponds to an anomaly label\. For example, the color for the `scratch` anomaly label in the following JSON line is `#2ca02c`\.

```
{
    "source-ref": "s3://path-to-image",
    "anomaly-label": 1,
    "anomaly-label-metadata": {
        "class-name": "anomaly",
        "creation-date": "2021-10-12T14:16:45.668",
        "human-annotated": "yes",
        "job-name": "labeling-job/classification-job",
        "type": "groundtruth/image-classification",
        "confidence": 1
    },
    "anomaly-mask-ref": "s3://path-to-image",
    "anomaly-mask-ref-metadata": {
        "internal-color-map": {
            "0": {
                "class-name": "BACKGROUND",
                "hex-color": "#ffffff",
                "confidence": 0.0
            },
            "1": {
                "class-name": "scratch",
                "hex-color": "#2ca02c",
                "confidence": 0.0
            },
            "2": {
                "class-name": "dent",
                "hex-color": "#1f77b4",
                "confidence": 0.0
            }
        },
        "type": "groundtruth/semantic-segmentation",
        "human-annotated": "yes",
        "creation-date": "2021-11-23T20:31:57.758889",
        "job-name": "labeling-job/segmentation-job"
    }
}
```

If the colors in the mask image don't match the values in `hex-color`, training fails and you need to update the manifest file\. 

**To update the color values in a manifest file**

1. Using a text editor, open the manifest file that you used to create the dataset\.

1. For each JSON line \(image\), check that the colors \(`hex-color`\) within the `internal-color-map` field match the colors for the anomaly labels in the mask image\. 

   You can get location of the mask image from `anomaly-mask-ref` field\. Download the image to your computer and use the following code to get the colors in an image\. 

   ```
   from PIL import Image
   img = Image.open('path to local copy of mask file')
   colors = img.convert('RGB').getcolors() #this converts the mode to RGB
   for color in colors:    
       print('#%02x%02x%02x' % color[1])
   ```

1. For each image with an incorrect color assignment, update the `hex-color` field in the JSON line for the image\. 

1. Save the update manifest file\.

1. [Delete](delete-dataset.md) the existing dataset from the project\. 

1. [Create](create-dataset-use-manifest.md) a new dataset in the project with the updated manifest file\.

1. [Train](model-train.md) the model\.

Alternatively, for steps 5 and 6, you can update individual images in the dataset by calling the [UpdateDatasetEntries](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_UpdateDatasetEntries.html) operation and supplying updated JSON lines for the images you want to update\. For example code, see [Adding more images \(SDK\)](edit-dataset.md#add-more-images-dataset-sdk)\.

## Mask images aren't in PNG format<a name="mask-image-format"></a>

If you are training an image segmentation model, the mask images must be in PNG format\. If you create a dataset from a manifest file, make sure the mask images you reference in the `anomaly-mask-ref` are PNG format\. If the mask images are not in PNG format, you need to convert them to PNG format\. It is not sufficient to rename the extension for an image file to `.png`\.

Mask images that you create in the Amazon Lookout for Vision console or with a SageMaker Ground Truth job are created in PNG format\. You do not need to change the format of these images\. 

**To correct non\-PNG format mask images in a manifest file**

1. Using a text editor, open the manifest file that you used to create the dataset\.

1. For each JSON lines \(image\) make image sure that the `anomaly-mask-ref` references a PNG format image\. For more information, see [Creating a manifest file](manifest-files.md)\.

1. Save the updated manifest file\.

1. [Delete](delete-dataset.md) the existing dataset from the project\. 

1. [Create](create-dataset-use-manifest.md) a new dataset in the project with the updated manifest file\.

1. [Train](model-train.md) the model\.

## Segmentation or classification labels are inaccurate or missing<a name="missing-inaccurate-labels"></a>

Missing or inaccurate labels can cause training to fail or create a model that performs poorly\. We recommend that you label all images in your dataset\. If you don't label all images and model training fails, or your model performs poorly, add more images\.

Check the following:
+ If you are creating a segmentation model, masks must tightly cover the anomalies on your dataset images\. To check the masks in your dataset, view the images in the project's dataset gallery\. If necessary, redraw the image masks\. For more information, see [Segmenting images \(console\)](segment-image.md)\.
+ Make sure that anomalous images in your dataset images are classified\. If you're creating an image segmentation model, make sure anomalous images have anomaly labels and image masks\.

  

  It's important to remember which type of model \([segmentation](understanding.md#ud-image-segmentation) or [classification](understanding.md#ud-image-classification)\) you are creating\. A classification model doesn't require image masks on anomalous images\. Don't add masks to dataset images intended for a classification model\.

**To update missing labels**

  1. [Open](view-datasets.md) the project's dataset gallery\.

  1. Filter **Unlabeled** images to see which images do not have labels\.

  1. Do one of the following:
     + If you are creating an image classification model, [classify](model-label.md) each unlabeled image\.
     + If you are creating an image segmentation model, [classify and segment](segment-image.md) each unlabeled image\.

  1. If you are creating an image segmentation model, [add](segment-image.md#segment-image-annotation-tool) masks to any classified anomalous images that are missing masks\.

  1. [Train](model-train.md) the model\.

If you choose not to fix poor or missing labels, we recommend that you add more labeled images or remove the affected images from the dataset\. You can add more from the console or by using the [UpdateDatasetEntries](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_UpdateDatasetEntries.html) operation\. For more information, see [Adding images to your dataset](edit-dataset.md)\.

If you choose to remove the images, you must recreate the dataset without the affected images, as you can't delete an image from a dataset\. For more information, see [Removing images from your dataset](edit-dataset-remove-images.md)\.