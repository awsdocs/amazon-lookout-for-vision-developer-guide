# Detecting anomalies in an image<a name="inference-detect-anomalies"></a>

To detect anomalies in an image with a trained Amazon Lookout for Vision model, you call the [DetectAnomalies](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_DetectAnomalies) operation\. The result from `DetectAnomalies` includes a Boolean prediction that classifies the image as containing one or more anomalies and a confidence value for the prediction\. If the model is an image segmentation model, the result also includes a colored mask showing the positions of different types of anomalies\. 

**Note**  
Before calling `DetectAnomalies`, you must first start your model with the `StartModel` operation\. For more information, see [Starting your Amazon Lookout for Vision model](run-start-model.md)\. You are charged for the amount of time, in minutes, that a model runs and for the number of anomaly detection units that your model uses\. If you are not using a model, use the `StopModel` operation to stop your model\. For more information, see [Stopping your Amazon Lookout for Vision model](run-stop-model.md)\.

**Topics**
+ [Calling DetectAnomalies](#inference-calling-detect-anomalies)
+ [Understanding the response from DetectAnomalies](#inference-response)
+ [Determining if an image is anomalous](inference-determine-anomaly-state.md)
+ [Showing classification and segmentation information](inference-display-information.md)

## Calling DetectAnomalies<a name="inference-calling-detect-anomalies"></a>

To call `DetectAnomalies`, specify the following:
+ **Project** – The name of the project that contains the model that you want to use\.
+ **ModelVersion** – The version of the model that you want to use\.
+ **ContentType** – The type of image that you want analyze\. Valid values are `image/png` \(PNG format images\) and `image/jpeg` \(JPG format images\)\. 
+ **Body** – The unencoded binary bytes that represent the image\.

  The image must have the same dimensions as the images used to train the model\.

The following example shows how to call `DetectAnomalies`\. You can use the function response from the Python and Java examples to call functions in [Determining if an image is anomalous](inference-determine-anomaly-state.md)\.

------
#### [ AWS CLI ]

This AWS CLI command displays the JSON output for the `DetectAnomalies` CLI operation\. Change the values of the following input parameters: 
+ `project name` with the name of the project that you want to use\. 
+ `model version` with the version of the model that you want to use\.
+ `content type` with the type of the image that you want to use\. Valid values are `image/png` \(PNG format images\) and `image/jpeg` \(JPG format images\)\. 
+ `file name` with the path and file name of the image that you want to use\. Ensure that the file type matches the value of `content-type`\. 

```
aws lookoutvision detect-anomalies  --project-name project name\
    --model-version model version\
    --content-type content type\
    --body file name \
    --profile lookoutvision-access
```

------
#### [ Python ]

For the complete code example, see [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/python/example_code/lookoutvision/inference.py)\.

```
    def detect_anomalies(lookoutvision_client, project_name, model_version, photo):
        """
        Calls DetectAnomalies using the supplied project, model version, and image.
        :param lookoutvision_client: A Lookout for Vision Boto3 client.
        :param project: The project that contains the model that you want to use.
        :param model_version: The version of the model that you want to use.
        :param photo: The photo that you want to analyze.
        :return: The DetectAnomalyResult object that contains the analysis results.
        """

        image_type = imghdr.what(photo)
        if image_type == "jpeg":
            content_type = "image/jpeg"
        elif image_type == "png":
            content_type = "image/png"
        else:
            logger.info("Invalid image type for %s", photo)
            raise ValueError(
                f"Invalid file format. Supply a jpeg or png format file: {photo}")


        # Get images bytes for call to detect_anomalies
        with open(photo, "rb") as image:
            response = lookoutvision_client.detect_anomalies(
                ProjectName=project_name,
                ContentType=content_type,
                Body=image.read(),
                ModelVersion=model_version)

        return response['DetectAnomalyResult']
```

------
#### [ Java V2 ]

```
   public static DetectAnomalyResult detectAnomalies(LookoutVisionClient lfvClient, String projectName,
            String modelVersion,
            String photo) throws IOException, LookoutVisionException {
        /**
         * Creates an Amazon Lookout for Vision dataset from a manifest file.
         * Returns after Lookout for Vision creates the dataset.
         * 
         * @param lfvClient    An Amazon Lookout for Vision client.
         * @param projectName  The name of the project in which you want to create a
         *                     dataset.
         * @param modelVersion The version of the model that you want to use.
         *
         * @param photo        The photo that you want to analyze.
         * 
         * @return DetectAnomalyResult The analysis result from DetectAnomalies.
         */

        logger.log(Level.INFO, "Processing local file: {0}", photo);

        // Get image bytes.

        InputStream sourceStream = new FileInputStream(new File(photo));
        SdkBytes imageSDKBytes = SdkBytes.fromInputStream(sourceStream);
        byte[] imageBytes = imageSDKBytes.asByteArray();

        // Get the image type. Can be image/jpeg or image/png.
        String contentType = getImageType(imageBytes);

        // Detect anomalies in the supplied image.
        DetectAnomaliesRequest request = DetectAnomaliesRequest.builder().projectName(projectName)
                .modelVersion(modelVersion).contentType(contentType).build();

        DetectAnomaliesResponse response = lfvClient.detectAnomalies(request,
                RequestBody.fromBytes(imageBytes));

        /*
         * Tip: You can also use the following to analyze a local file.
         * Path path = Paths.get(photo);
         * DetectAnomaliesResponse response = lfvClient.detectAnomalies(request, path);
         */
        DetectAnomalyResult result = response.detectAnomalyResult();

        String prediction = "Prediction: Normal";

        if (Boolean.TRUE.equals(result.isAnomalous())) {
            prediction = "Prediction: Anomalous";
        }

        // Convert confidence to percentage.
        NumberFormat defaultFormat = NumberFormat.getPercentInstance();
        defaultFormat.setMinimumFractionDigits(1);
        String confidence = String.format("Confidence: %s", defaultFormat.format(result.confidence()));

        // Log classification result.
        String photoPath = "File: " + photo;
        String[] imageLines = { photoPath, prediction, confidence };
        logger.log(Level.INFO, "Image: {0}\nAnomalous: {1}\nConfidence {2}", imageLines);

        return result;

    }

    // Gets the image mime type. Supported formats are image/jpeg and image/png.
    private static String getImageType(byte[] image) throws IOException {

        InputStream is = new BufferedInputStream(new ByteArrayInputStream(image));
        String mimeType = URLConnection.guessContentTypeFromStream(is);

        logger.log(Level.INFO, "Image type: {0}", mimeType);

        if (mimeType.equals("image/jpeg") || mimeType.equals("image/png")) {
            return mimeType;
        }
        // Not a supported file type.
        logger.log(Level.SEVERE, "Unsupported image type: {0}", mimeType);
        throw new IOException(String.format("Wrong image type. %s format isn't supported.", mimeType));
    }
```

------

## Understanding the response from DetectAnomalies<a name="inference-response"></a>

The response from `DetectAnomalies` varies depending on the type of the model that you train \(classification model or segmentation model\)\. In both cases the response is a [DetectAnomalyResult](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_DetectAnomalyResult) object\. 

### Classification model<a name="inference-response-classification"></a>

If your model is an [Image classification model](understanding.md#ud-image-classification), the response from `DetectAnomalies` contains the following: 
+ **IsAnomalous**– A Boolean indicator that the image contains one or more anomalies\.
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

You determine if in an image is anomalous by checking the `IsAnomalous` field and confirming that the `Confidence` value is high enough for your needs\. 

If you're finding the confidence values returned by `DetectAnomalies` are too low, consider retraining the model\. For example code, see [Classification](inference-determine-anomaly-state.md#inference-classification)\.

### Segmentation model<a name="inference-response-segmentation"></a>

If your model is an [Image segmentation model](understanding.md#ud-image-segmentation), the response includes classification information and segmentation information, such as an image mask and anomaly types\. Classification information is calculated separately from segmentation information and you shouldn't assume a relationship between them\. For example code, see [Segmentation](inference-determine-anomaly-state.md#inference-segmentation) and [Showing classification and segmentation information](inference-display-information.md)\.
+ **IsAnomalous** \(classification\) – A Boolean indicator that classifies the image as either normal or anomalous\. 
+ **Confidence** \(classification\) – The confidence that Amazon Lookout for Vision has in the accuracy of the classification of the image \(`IsAnomalous`\)\. `Confidence` is a floating point value between 0 and 1\. A higher value indicates a higher confidence\.
+ **Source** – Information about the image passed to `DetectAnomalies`\.
+ **AnomalyMask** \(segmentation\) – A pixel mask covering anomalies found in the analyzed image\. There can be multiple anomalies on the image\. The color of a mask maps indicates the type of an anomaly\. The mask colors map to the colors assigned to anomaly types in the training dataset\. To find the anomaly type from a mask color, check `Color` in the `PixelAnomaly` field of each anomaly returned in the `Anomalies` list\. For example code, see [Showing classification and segmentation information](inference-display-information.md)\.
+ **Anomalies** \(segmentation\) – A list of anomalies found in the image\. Each anomaly includes the anomaly type \(`Name`\), and pixel information \(`PixelAnomaly`\)\. `TotalPercentageArea` is the percentage area of the image that the anomaly covers\. `Color` is the mask color for the anomaly\. 

  The first element in the list is always an anomaly type representing the image background \(`BACKGROUND`\) and shouldn't be considered an anomaly\. Amazon Lookout for Vision automatically adds the background anomaly type to the response\. You don't need to declare a background anomaly type in your dataset\.

```
{
    "DetectAnomalyResult": {
        "Source": {
            "Type": "direct"
        },
        "IsAnomalous": true,
        "Confidence": 0.9996814727783203,
        "Anomalies": [
            {
                "Name": "background",
                "PixelAnomaly": {
                    "TotalPercentageArea": 0.998999834060669,
                    "Color": "#FFFFFF"
                }
            },
            {
                "Name": "scratch",
                "PixelAnomaly": {
                    "TotalPercentageArea": 0.0004034999874420464,
                    "Color": "#7ED321"
                }
            },
            {
                "Name": "dent",
                "PixelAnomaly": {
                    "TotalPercentageArea": 0.0005966666503809392,
                    "Color": "#4DD8FF"
                }
            }
        ],
        "AnomalyMask": "iVBORw0....."
    }
}
```