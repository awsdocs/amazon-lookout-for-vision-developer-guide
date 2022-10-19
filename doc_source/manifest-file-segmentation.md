# Defining JSON lines for image segmentation<a name="manifest-file-segmentation"></a>

You define a JSON line for each image that you want to use in an Amazon Lookout for Vision manifest file\. If you want to create a segmentation model, The JSON line must include segmentation and classification information for the image\.  A manifest file is made of one or more JSON lines, one for each image that you want to import\. 

**To create a manifest file for segmented images**

1. Create an empty text file\.

1. Add a JSON line for each image the that you want to import\. Each JSON line should look similar to the following:

   ```
   {"source-ref":"s3://path-to-image","anomaly-label":1,"anomaly-label-metadata":{"class-name":"anomaly","creation-date":"2021-10-12T14:16:45.668","human-annotated":"yes","job-name":"labeling-job/classification-job","type":"groundtruth/image-classification","confidence":1},"anomaly-mask-ref":"s3://path-to-image","anomaly-mask-ref-metadata":{"internal-color-map":{"0":{"class-name":"BACKGROUND","hex-color":"#ffffff","confidence":0.0},"1":{"class-name":"scratch","hex-color":"#2ca02c","confidence":0.0},"2":{"class-name":"dent","hex-color":"#1f77b4","confidence":0.0}},"type":"groundtruth/semantic-segmentation","human-annotated":"yes","creation-date":"2021-11-23T20:31:57.758889","job-name":"labeling-job/segmentation-job"}}
   ```

1. Save the file\. 
**Note**  
You can use the extension `.manifest`, but it is not required\. 

1. Create a dataset using the manifest file that you created\. For more information, see [Creating a manifest file](manifest-files.md)\. 

## Segmentation JSON lines<a name="cd-manifest-segmentation-json"></a>

In this section, you learn how to create a JSON line that includes segmentation and classification information for an image\.

The following JSON line shows an image with segmentation and classification information\. `anomaly-label-metadata` contains classification information\. `anomaly-mask-ref` and `anomaly-mask-ref-metadata` contain segmentation information\.

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

## JSON line keys and values<a name="json-line-format-segmentation"></a>

The following information describes the keys and values in an Amazon Lookout for Vision JSON line\.

### source\-ref<a name="json-line-format-segmentation-source-ref.title"></a>

\(Required\) The Amazon S3 location of the image\. The format is `"s3://BUCKET/OBJECT_PATH"`\. Images in an imported dataset must be stored in the same Amazon S3 bucket\. 

### anomaly\-label<a name="json-line-format-segmentation-anomaly-label"></a>

\(Required\) The label attribute\. Use the key `anomaly-label`, or another key name that you choose\. The key value \(`1` in the preceding example\) is required by Amazon Lookout for Vision, but it isn't used\. The output manifest created by Amazon Lookout for Vision converts the value to `1` for an anomalous image and a value of `0` for a normal image\. The value of `class-name` determines if the image is normal or anomalous\. 

There must be corresponding metadata identified by the field name with *\-metadata* appended\. For example, `"anomaly-label-metadata"`\. 

### anomaly\-label\-metadata<a name="json-line-format-segmentation-anomaly-label-metadata"></a>

\(Required\) Metadata about the label attribute\. Contains classification information\. The field name must be the same as the label attribute with *\-metadata* appended\. 

*confidence*  
\(Optional\) Currently not used by Amazon Lookout for Vision\. If you do specify a value, use a value of `1`\. 

*job\-name*  
\(Optional\) A name that you choose for the job that processes the image\. 

*class\-name*  
\(Required\) If the image contains normal content, specify `normal`, otherwise specify `anomaly`\. If the value of `class-name` is any other value, the image is added to the dataset as an unlabeled image\. To label an image, see [Adding images to your dataset](edit-dataset.md)\. 

*human\-annotated*  
\(Required\) Specify `"yes"`, if the annotation was completed by a human\. Otherwise, specify `"no"`\. 

*creation\-date*   
\(Optional\) The Coordinated Universal Time \(UTC\) date and time that the label was created\. 

*type*  
\(Required\) The type of processing that should be applied to the image\. Use the value `"groundtruth/image-classification"`\. 

### anomaly\-mask\-ref<a name="json-line-format-segmentation-anomaly-mask-ref"></a>

\(Required\) The Amazon S3 location of the mask image\. Use `anomaly-mask-ref` for the key name or use a key name of your choosing\. The key must end with `-ref`\. The mask image must contain colored masks for each anomaly type `internal-color-map`\. The format is `"s3://BUCKET/OBJECT_PATH"`\. Images in an imported dataset must be stored in the same Amazon S3 bucket\. The mask image must be a Portable Network Graphic \(PNG\) format image\.

### anomaly\-mask\-ref\-metadata<a name="json-line-format-segmentation-anomaly-mask-ref-metadata"></a>

\(Required\) Segmentation metadata for the image\. Use `anomaly-mask-ref-metadata` for the key name or use a key name of your choosing\. The key name must end with `-ref-metadata`\. 

*internal\-color\-map*  
\(Required\) A map of colors that map to individual anomaly types\. The colors must match the colors in the mask image \(`anomaly-mask-ref`\)\.    
*key*  
\(Required\) The key into the map\. The entry `0` must contain the class\-name BACKGROUND that represents areas outside of anomalies on the image\.    
*class\-name*  
\(Required\) The name of the anomaly type, such as scratch or dent\.  
hex\-color  
\(Required\) The hex color for the anomaly type, such as `#2ca02c`\. The color must match a color in `anomaly-mask-ref`\. The value for the `BACKGROUND` anomaly type is always `#ffffff`\.  
confidence  
\(Required\) Currently not used by Amazon Lookout for Vision, but a float value is required\. 

*human\-annotated*  
\(Required\) Specify `"yes"`, if the annotation was completed by a human\. Otherwise, specify `"no"`\. 

*creation\-date*   
\(Optional\) The Coordinated Universal Time \(UTC\) date and time that the segmentation information was created\. 

*type*  
\(Required\) The type of processing that should be applied to the image\. Use the value `"groundtruth/semantic-segmentation"`\. 