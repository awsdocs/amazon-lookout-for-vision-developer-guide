# Detecting anomalies in an image<a name="inference-detect-anomalies"></a>

To detect anomalies in an image with a trained Amazon Lookout for Vision model, you call the [DetectAnomalies](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_DetectAnomalies) operation\. The result from `DetectAnomalies` includes a Boolean prediction that classifies the image as containing one or more anomalies and a confidence value for the prediction\. If the model is an image segmentation model, the result also includes a colored mask showing the positions of different types of anomalies\. 

**Note**  
Before calling `DetectAnomalies`, you must first start your model with the `StartModel` operation\. For more information, see [Starting your Amazon Lookout for Vision model](run-start-model.md)\. You are charged for the amount of time, in minutes, that a model runs and for the number of anomaly detection units that your model uses\. If you are not using a model, use the `StopModel` operation to stop your model\. For more information, see [Stopping your Amazon Lookout for Vision model](run-stop-model.md)\.

**Topics**
+ [Calling DetectAnomalies](#inference-calling-detect-anomalies)
+ [Understanding the response from DetectAnomalies](#inference-response)
+ [Determining if an image is anomalous](#inference-determine-anomaly-state)
+ [Showing classification and segmentation information](#inference-display-information)

## Calling DetectAnomalies<a name="inference-calling-detect-anomalies"></a>

To call `DetectAnomalies`, specify the following:
+ **Project** – The name of the project that contains the model that you want to use\.
+ **ModelVersion** – The version of the model that you want to use\.
+ **ContentType** – The type of image that you want analyze\. Valid values are `image/png` \(PNG format images\) and `image/jpeg` \(JPG format images\)\. 
+ **Body** – The unencoded binary bytes that represent the image\.

  The image must have the same dimensions as the images used to train the model\.

The following example shows how to call `DetectAnomalies`\. You can use the function response from the Python and Java examples to call functions in [Determining if an image is anomalous](#inference-determine-anomaly-state)\.

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
    --body file name
```

------
#### [ Python ]

```
def detect_anomalies(project_name,model_version,photo):
    """
    Calls DetectAnomalies using the suplied project, model version, and image.
    :param project: The project that contains the model that you want to use.
    :param model_version: The version of the model that you want to use.
    :param photo: The photo that you want to analyze.
    :return: The DetectAnomalyResult object that contains the analysis results.
    """

    image=Image.open(photo) 
    image_type=Image.MIME[image.format]

    lookoutvision_client = boto3.client("lookoutvision")

    if (image_type == "image/jpeg" or image_type== "image/png") == False:
        logger.info("Invalid image type for %s", photo)
        raise ValueError(
            f"Invalid file format. Supply a jpeg or png format file: {photo}"
        )


    # get images bytes for call to detect_anomalies
    image_bytes = io.BytesIO()
    image.save(image_bytes, format=image.format)
    image_bytes = image_bytes.getvalue()

    # analyze the image
    response = lookoutvision_client.detect_anomalies(
            ProjectName=project_name,
            ContentType=image_type,  
            Body=image_bytes,
            ModelVersion=model_version,
        )
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

If you're finding the confidence values returned by `DetectAnomalies` are too low, consider retraining the model\.  

### Segmentation model<a name="inference-response-segmentation"></a>

If your model is an [Image segmentation model](understanding.md#ud-image-segmentation), the response includes classification information and segmentation information, such as an image mask and anomaly types\. Classification information is calculated separately from segmentation information and you shouldn't assume a relationship between them\.
+ **IsAnomalous** \(classification\) – A Boolean indicator that classifies the image as either normal or anomalous\. 
+ **Confidence** \(classification\) – The confidence that Amazon Lookout for Vision has in the accuracy of the classification of the image \(`IsAnomalous`\)\. `Confidence` is a floating point value between 0 and 1\. A higher value indicates a higher confidence\.
+ **Source** – Information about the image passed to `DetectAnomalies`\.
+ **AnomalyMask** \(segmentation\) – A pixel mask covering anomalies found in the analyzed image\. There can be multiple anomalies on the image\. The color of a mask maps indicates the type of an anomaly\. The mask colors map to the colors assigned to anomaly types in the training dataset\. To find the anomaly type from a mask color, check `Color` in the `PixelAnomaly` field of each anomaly returned in the `Anomalies` list\. For example code, see [Showing classification and segmentation information](#inference-display-information)\.
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

## Determining if an image is anomalous<a name="inference-determine-anomaly-state"></a>

You can determine if an image is anomalous in a variety of ways\. The method you choose depends on your use case and the type of your model\. The following are potential solutions\.

### Classification<a name="inference-classification"></a>

 `IsAnomalous` classifies an image as anomalous, use the `Confidence` field to help decide if the image is actually anomalous\. A higher value indicates greater confidence\. For example, you might decide a product is defective only if the confidence is over 80%\. You can classify images analyzed by classification models or by image segmentation models\.

------
#### [ Python ]

```
    def reject_on_classification(image, prediction, confidence_limit):
        """
        Returns True if the anomaly confidence is >= than the supplied confidence limit.
        :param image: The name of the image file that was analyzed.
        :param prediction: The DetectAnomalyResult object returned from DetectAnomalies
        :param confidence_limit: The minimum acceptable confidence. Float value between 0 and 1.
        :return: True if the error condition indicates an anomaly, otherwise False.
        """

        reject = False

        logger.info(f"Checking classification for {image}")

        if prediction['IsAnomalous'] and prediction['Confidence'] >= confidence_limit:
            reject = True
            logger.info(
                f"Rejected: Anomaly confidence ({prediction['Confidence']:.2%}) is greater"
                f" than limit ({confidence_limit:.2%})"
            )
        
        if reject is False:
            logger.info("No anomalies found.")
        return reject
```

------
#### [ Java V2 ]

```
    public static boolean rejectOnClassification(String photo, DetectAnomalyResult prediction, float minConfidence) {
        /**
         * Rejects an image based on its anomaly classification and prediction confidence
         * 
         * @param prediction   The prediction for an image analyzed with DetectAnomalies.
         * @param minConfidence   The minimum acceptable confidence for the prediction (0-1).
         * 
         * @return boolean True if the image is anomalous, otherwise False.
         */
        
        Boolean reject = false;
        
        logger.log(Level.INFO, "Checking classification for {0}", photo);
        
        String[] logParameters = { photo, prediction.confidence().toString(), String.valueOf(minConfidence) };
       

        if (prediction.isAnomalous() && prediction.confidence() >= minConfidence) {
            logger.log(Level.INFO, "Rejected: Anomaly confidence {0} is greater than confidence limit {1}", logParameters);
            reject = true;
        }
        if (!reject)
            logger.log(Level.INFO, ": No anomalies found.");
        
        return reject;
            
    }
```

------

### Segmentation<a name="inference-segmentation"></a>

If your model is an image segmentation model, you can use the segmentation information to determine if an image contains anomalies\. You can also use an image segmentation model to classify images\. 

#### Area of anomaly<a name="inference-segmentation-area"></a>

Use the percentage coverage \(`TotalPercentageArea`\) of an anomaly on the image\. For example, you might decide a product is defective if the area of an anomaly is greater than 1% of the image\. 

------
#### [ Python ]

```
    def reject_on_coverage(image, prediction, confidence_limit, anomaly_type, coverage_limit):
        """
        Checks if the coverage area of an anomaly is > than the coverage limit and if
        the prediction confidence > than the confidence limit. 
        :param image: The name of the image file that was analyzed.
        :param prediction: The DetectAnomalyResult object returned from DetectAnomalies
        :param confidence_limit: The minimum acceptable confidence. Float value between 0 and 1.
        :anomaly_type: The type of the anomaly that you want to check.
        :coverage_limit: The maximum acceptable percentage coverage of an anomaly (Float 0-1).
        :return: True if the error condition indicates an anomaly, otherwise False.
        """

        reject = False

        logger.info(f"Checking coverage for {image}")

        if prediction['IsAnomalous'] and prediction['Confidence'] >= confidence_limit:
            for anomaly in prediction['Anomalies']:
                if anomaly['Name'] == anomaly_type and anomaly['PixelAnomaly']['TotalPercentageArea'] > (coverage_limit/100):
                    reject = True
                    logger.info(
                        f"Rejected: Anomaly confidence ({prediction['Confidence']:.2%}) is greater than limit ({confidence_limit:.2%}) and \n"
                        f"{anomaly['Name']} coverage ({anomaly['PixelAnomaly']['TotalPercentageArea']:.2%}) "
                        f"is greater than limit ({coverage_limit/100:.2%}) ")
        
        if reject is False:
            logger.info("No anomalies found.")

        return reject
```

------
#### [ Java V2 ]

```
    private Boolean rejectOnCoverage(String photo,DetectAnomalyResult prediction, float minConfidence,
            String anomalyType, float maxCoverage ) {
        /**
         * Rejects an image based on a maximum allowable coverage area for an anomaly type.
         * 
         * @param prediction   The prediction for an image analyzed with DetectAnomalies.
         * @param minConfidence   The minimum acceptable confidence for the prediction (0-1).
         * @param anomalyTypes The anomaly type to check.
         * @param maxCoverage The maximum allowable coverage area of the anomaly type. (0-1). 
         * 
         * @return boolean True if the coverage area of the anomaly type exceeds the maximum allowed, otherwise False.
         */

        Boolean reject = false;
        
        logger.log(Level.INFO, "Checking coverage for {0}", photo);
        
        
        if (prediction.isAnomalous() && prediction.confidence() >= minConfidence) {
          for (Anomaly anomaly : prediction.anomalies()) {
              
              
            if (anomaly.name() == anomalyType && anomaly.pixelAnomaly().totalPercentageArea() >= maxCoverage ) {
                
                String[] logParameters = {  prediction.confidence().toString(), 
                        String.valueOf(minConfidence), 
                        String.valueOf(anomaly.pixelAnomaly().totalPercentageArea()),
                        String.valueOf(maxCoverage)};
                logger.log(Level.INFO, "Rejected: Anomaly confidence {0} is greater than confidence limit {1} and " +
                        "{2} anomaly type coverage is than coverage limit {3}\n", logParameters);
                reject = true;
                    
            }
          }
        }
        
        if (!reject)
            logger.log(Level.INFO, ": No anomalies found.");
        
        return reject;
      }
```

------

#### Number of anomaly types<a name="inference-segmentation-number-of-anomaly-types"></a>

Use a count of different anomaly types \(`Name`\) found on the image\. For example, you might decide a product is defective if there is more than two types of anomaly present\. 

------
#### [ Python ]

```
    def reject_on_anomaly_types(image, prediction, confidence_limit, anomaly_types_limit):
        """
        Checks if the number of anomaly types > than the anomaly types limit and if
        the prediction confidence > than the confidence limit. 
        :param image: The name of the image file that was analyzed.
        :param prediction: The DetectAnomalyResult object returned from DetectAnomalies
        :param confidence: The minimum acceptable confidence. Float value between 0 and 1.
        :param anomaly_types_limit: The maximum number of allowable anomaly types.
        :return: True if the error condition indicates an anomaly, otherwise False.
        """

        logger.info(f"Checking number of anomaly types for {image}")

        anomaly_types = set()
        reject = False

        if prediction['IsAnomalous'] and prediction['Confidence'] >= confidence_limit:
            for anomaly in prediction['Anomalies']:
                anomaly_types.add(anomaly['Name'])

            # Reduce by one to account for 'background' anomaly type.
            if (len(anomaly_types)-1) > anomaly_types_limit:
                reject = True
                logger.info(
                        f"Rejected: Anomaly confidence ({prediction['Confidence']:.2%}) is greater than limit ({confidence_limit:.2%}) and \n"
                        f"the number of anomaly types ({len(anomaly_types)-1}) is greater than the limit ({anomaly_types_limit}) ")
        
        if reject is False:
            logger.info("No anomalies found.")
        return reject
```

------
#### [ Java V2 ]

```
    private Boolean rejectOnAnomalyTypeCount(String photo,DetectAnomalyResult prediction,
            float minConfidence, Integer maxAnomalyTypes ) {
        
        /**
         * Rejects an image based on a maximum allowable number of anomaly types.
         * 
         * @param prediction   The prediction for an image analyzed with DetectAnomalies.
         * @param minConfidence   The minimum acceptable confidence for the predictio (0-1).
         * @param maxAnomalyTypes The maximum allowable number of anomaly types.
         * 
         * @return boolean True if the image contains more than the maximum allowed anomaly types, otherwise False.
         */

        Boolean reject = false;
        
        logger.log(Level.INFO, "Checking coverage for {0}", photo);
        
        Set<String> defectTypes = new HashSet<>();
 
        if (prediction.isAnomalous() && prediction.confidence() >= minConfidence) {
          for (Anomaly anomaly : prediction.anomalies()) {
              defectTypes.add(anomaly.name());
          }     
          // Reduce defect types by one to account for 'background' anomaly type.   
          if ((defectTypes.size()-1) > maxAnomalyTypes) {
              String[] logParameters = {  prediction.confidence().toString(), 
                      String.valueOf(minConfidence), 
                      String.valueOf(defectTypes.size()),
                      String.valueOf(maxAnomalyTypes)};
              logger.log(Level.INFO, "Rejected: Anomaly confidence {0} is >= minimum confidence {1} and " +
                      "the number of anomaly types {2} > the allowable number of anomaly types {3}\n", logParameters); 
              reject = true;
          }
         
        }
        
        if (!reject)
            logger.log(Level.INFO, ": No anomalies found.");
        
        return reject;
      }
```

------

## Showing classification and segmentation information<a name="inference-display-information"></a>

This example shows the analyzed image and overlays the analysis results\. If the response includes an anomaly mask, the mask is shown in the colors of the associated anomaly types\.

**To show image classification and image segmentation information**

1. If you haven't already done so, do the following:

   1. Create or update an IAM user\.  For more information, see [Step 2: Create an IAM administrator user and group](su-account-user.md)\.

   1. Install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 5: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

   1. [Train your model](model.md)\.

   1. [Start your model](run-start-model.md)\.

1. Ensure the IAM user calling `DetectAnomalies` has access to the model version that you want to use\. 

1. Use the following code\.

------
#### [ Python ]

   The following example code detects anomalies in an image that you supply\. The example takes the following command line options: 
   + `project` – the name of the project that you want to use\. 
   + `version` – the version of the model, within the project, that you want to use\.
   + `image` – the path and file of a local image file \(JPEG or PNG format\)\. 

   ```
   # Copyright Amazon.com, Inc. or its affiliates. All Rights Reserved.
   # SPDX-License-Identifier: Apache-2.0
   """
   Uses a trained Amazon Lookout for Vision model to detect anomalies
   in an image. The image can be local or in an S3 bucket.
   """
   
   import argparse
   import logging
   import io
   import boto3
   from PIL import Image, ImageDraw, ImageFont
   
   from PIL.Image import Image as ImageObj
   
   
   from botocore.exceptions import ClientError
   
   logger = logging.getLogger(__name__)
   
   
   class Inference:
       """
       Shows how to detect anomalies in an image using a trained Amazon Lookout
       for Vision model.
       """
   
       @staticmethod
       def show_anomaly_prediction(lookoutvision_client, project_name,model_version,photo):
           """
           Detects anomalies in an image (jpg/png) by using your Amazon Lookout for Vision
           model. Displays the image and overlays prediction information text.
           :param lookoutvision_client: An Amazon Lookout for Vision Boto3 client.
           :param project_name: The name of the project that contains the model that
           you want to use.
           :param model_version: The version of the model that you want to use.
           :param photo: The path and name of the image in which you want to detect
           anomalies.
           """
           try:
          
               image=Image.open(photo) 
               image_type=Image.MIME[image.format]
   
               if (image_type == "image/jpeg" or image_type== "image/png") == False:
                   logger.info("Invalid image type for %s", photo)
                   raise ValueError(
                       f"Invalid file format. Supply a jpeg or png format file: {photo}"
                   )
              
               # get images bytes for call to detect_anomalies
               image_bytes = io.BytesIO()
               image.save(image_bytes, format=image.format)
               image_bytes = image_bytes.getvalue()
   
               # analyze the image
               response = lookoutvision_client.detect_anomalies(
                       ProjectName=project_name,
                       ContentType=image_type,  
                       Body=image_bytes,
                       ModelVersion=model_version,
                   )
               
               logger.info("Detecting anomalies in %s", photo)
   
               prediction = "Normal"
               if (response["DetectAnomalyResult"]["IsAnomalous"]):
                       prediction="Abnormal"
   
               confidence=response["DetectAnomalyResult"]["Confidence"]
               # logger.info(response)
               found_anomalies = response["DetectAnomalyResult"]['Anomalies']
   
               image_mask_bytes = response["DetectAnomalyResult"]["AnomalyMask"]
   
               image_mask = Image.open(io.BytesIO(image_mask_bytes))
   
               final_img = Image.blend(image, image_mask, 0.5) if prediction=="Abnormal" else image
   
               logger.info("Prediction: %s", format(prediction))
               logger.info("Confidence: %s", format(confidence))
   
               colors = []
               color_index = 0
               print_lines = []
               print_lines.append("Classification:")
               print_lines.append(f"  Prediction: {prediction}")
               print_lines.append(f"  Confidence: {confidence:.2%}")
               print_lines.append("\n")
               if (len(found_anomalies)) > 1:
                   logger.info("Anomalies:")
                   print_lines.append("Segmentation:")
                   for i in range(1,len(found_anomalies)):
                       logger.info(f"  {found_anomalies[i]['Name']}")
                       logger.info(f"    Color: {found_anomalies[i]['PixelAnomaly']['Color']}")
                       logger.info(f"    Area: {round(found_anomalies[i]['PixelAnomaly']['TotalPercentageArea'] * 100,2)}%")
                       print_lines.append(f"  Name: {found_anomalies[i]['Name']}, Area: {round(found_anomalies[i]['PixelAnomaly']['TotalPercentageArea'] * 100,2)}%")
                       colors.append(found_anomalies[i]['PixelAnomaly']['Color'])
   
               draw = ImageDraw.Draw(final_img)  
               
               font_size=50
   
               fnt = ImageFont.truetype('/Library/Fonts/Tahoma.ttf', font_size)
               y1=0
               
               # Draw black box and overlay text
               for i in range(0, len(print_lines)):
   
                   text = print_lines[i]
   
                   fill = "white"
                   if i > len(print_lines)-3 and len(colors) > 0:
                       fill = colors[color_index]
                       color_index+=1
                   
                   text_width, text_height = draw.textsize(text,fnt)
   
                   y2 = y1 + text_height 
                 
                   draw.rectangle([(10 , y1), (text_width + 10, y2)],fill="black")
                   draw.text((10,y1), text, fill=fill, font=fnt)  
                   
                   y1+=text_height
   
               final_img.show()
       
           except ClientError as err:
               logger.info(format(err))
               raise
   
   def add_arguments(parser):
       """
       Adds command line arguments to the parser.
       :param parser: The command line parser.
       """
   
       parser.add_argument(
           "project", help="The project containing the model that you want to use."
       )
       parser.add_argument(
           "version", help="The version of the model that you want to use."
       )
       parser.add_argument(
           "image",
           help="The file that you want to analyze. "
           "Supply a local file path.",
       )
   
   def main():
       """
       Entrypoint for anomaly detection example.
       """
   
       try:
           logging.basicConfig(level=logging.INFO, format="%(levelname)s: %(message)s")
   
           lookoutvision_client = boto3.client("lookoutvision")
   
           parser = argparse.ArgumentParser(usage=argparse.SUPPRESS)
   
           add_arguments(parser)
   
           args = parser.parse_args()
   
           # analyze image
           Inference.show_anomaly_prediction(
               lookoutvision_client, args.project, args.version, args.image
           )
   
       except ClientError as err:
           print("A service error occured: " + format(err.response["Error"]["Message"]))
       except FileNotFoundError as err:
           print("The supplied file couldn't be found: " + err.filename)
       except ValueError as err:
           print("A value error occured. " + format(err))
       else:
           print("Successfully completed analysis.")
   
   if __name__ == "__main__":
       main()
   ```

------
#### [ Java 2 ]

   The following example displays the input image and overlays the file name, the anomaly prediction, and the prediction confidence\. The example takes the following command line options: 
   + `project` – the name of the project that you want to use\. 
   + `version` – the version of the model, within the project, that you want to use\.
   + `image` – the path and file of a local image file \(JPEG or PNG format\)\. 

   ```
   //Copyright 2021 Amazon.com, Inc. or its affiliates. All Rights Reserved.
   //PDX-License-Identifier: MIT-0 (For details, see https://github.com/awsdocs/amazon-lookout-for-vision-developer-guide/blob/main/LICENSE-SAMPLECODE.)
   
   package com.example.lookoutvision;
   
   import software.amazon.awssdk.core.SdkBytes;
   import software.amazon.awssdk.core.sync.RequestBody;
   import software.amazon.awssdk.services.lookoutvision.LookoutVisionClient;
   import software.amazon.awssdk.services.lookoutvision.model.DetectAnomaliesRequest;
   import software.amazon.awssdk.services.lookoutvision.model.DetectAnomaliesResponse;
   import software.amazon.awssdk.services.lookoutvision.model.DetectAnomalyResult;
   import software.amazon.awssdk.services.lookoutvision.model.LookoutVisionException;
   
   import java.io.BufferedInputStream;
   import java.io.ByteArrayInputStream;
   import java.io.File;
   import java.io.FileInputStream;
   import java.io.FileNotFoundException;
   import java.io.IOException;
   import java.io.InputStream;
   import java.net.URLConnection;
   
   import java.text.NumberFormat;
   import java.awt.*;
   import java.awt.font.LineMetrics;
   import java.awt.image.BufferedImage;
   import javax.imageio.ImageIO;
   import javax.swing.*;
   
   import java.util.logging.Level;
   import java.util.logging.Logger;
   
   // Finds anomalies on a supplied image
   public class DetectAnomalies extends JPanel {
   
       private static final long serialVersionUID = 1L;
       private transient BufferedImage image;
       private transient Dimension dimension;
       public static final Logger logger = Logger.getLogger(DetectAnomalies.class.getName());
   
       // Constructor. Finds anomalies in a local image file.
       public DetectAnomalies(LookoutVisionClient lfvClient, String projectName, String modelVersion,
               String photo) throws IOException, LookoutVisionException {
   
           logger.log(Level.INFO, "Processing local file: {0}", photo);
           
              
           // Get image bytes and buffered image
           InputStream sourceStream = new FileInputStream(new File(photo));
           SdkBytes imageSDKBytes = SdkBytes.fromInputStream(sourceStream);
           byte[] imageBytes = imageSDKBytes.asByteArray();
           ByteArrayInputStream inputStream = new ByteArrayInputStream(imageSDKBytes.asByteArray());
           image = ImageIO.read(inputStream);
   
           //Get the image type. Can be image/jpeg or image/png.
           String contentType=getImageType(imageBytes);
           
   
           // Set the size of the window that shows the image
           setWindowDimensions();
   
           //Detect anomalies in the supplied image.
           DetectAnomaliesRequest request = DetectAnomaliesRequest.builder().projectName(projectName)
                   .modelVersion(modelVersion).contentType(contentType).build();
   
   
           DetectAnomaliesResponse response = lfvClient.detectAnomalies(request,
                RequestBody.fromBytes(imageBytes));
   
           /* Tip: You can also use the following to analyze a local file
           Path path = Paths.get(photo);
           DetectAnomaliesResponse response = lfvClient.detectAnomalies(request, path);
           */
           DetectAnomalyResult result = response.detectAnomalyResult();
   
           String prediction = "Prediction: Normal";
   
           if (Boolean.TRUE.equals(result.isAnomalous())) {
               prediction = "Prediction: Abnormal";
           }
   
           // Convert prediction to percentage
           NumberFormat defaultFormat = NumberFormat.getPercentInstance();
           defaultFormat.setMinimumFractionDigits(1);
           String confidence = String.format("Confidence: %s", defaultFormat.format(result.confidence()));
   
           // Draw file name, prediction, and confidence on image
           String photoPath = "File: " + photo;
           String[] imageLines = { photoPath, prediction, confidence };
           drawImageInfo(imageLines);
   
           logger.log(Level.INFO, "Image: {0}\nAnomalous: {1}\nConfidence {2}", imageLines);
   
       }
   
       // Sets window dimensions to 1/2 screen size, unless image is smaller
       public void setWindowDimensions() {
           dimension = java.awt.Toolkit.getDefaultToolkit().getScreenSize();
   
           dimension.width = (int) dimension.getWidth() / 2;
           if (image.getWidth() < dimension.width) {
               dimension.width = image.getWidth();
           }
           dimension.height = (int) dimension.getHeight() / 2;
   
           if (image.getHeight() < dimension.height) {
               dimension.height = image.getHeight();
           }
   
           setPreferredSize(dimension);
   
       }
   
       // Draws the file name, prediction, and confidence on the image.
       public void drawImageInfo(String[] imageLines) {
   
           int indent = 10;
   
           // Set up drawing
           Graphics2D g2d = image.createGraphics();
           g2d.setColor(Color.GREEN);
           g2d.setFont(new Font("Tahoma", Font.PLAIN, 80));
           Font font = g2d.getFont();
           FontMetrics metrics = g2d.getFontMetrics(font);
   
           int y1=0;
   
             for (int i = 0; i < imageLines.length; i++) {
   
               // Get text height, width, and descent
               int textWidth=metrics.stringWidth(imageLines[i]);
               LineMetrics lm=metrics.getLineMetrics(imageLines[i], g2d);
               int textHeight = (int)lm.getHeight();
               int descent=(int)lm.getDescent();
   
               int y2=(y1+textHeight)-descent;
   
               // Draw black rectangle
               g2d.setColor(Color.BLACK);
               g2d.fillRect(indent, y1, textWidth, textHeight);
   
               // Draw text
               g2d.setColor(Color.GREEN);
               g2d.drawString(imageLines[i], indent, y2);
   
               y1+=textHeight;
   
           }
           g2d.dispose();
   
       }
   
       // Draws the image containing the bounding boxes and labels.
       @Override
       public void paintComponent(Graphics g) {
   
           Graphics2D g2d = (Graphics2D) g; // Create a Java2D version of g.
   
           // Draw the image.
           g2d.drawImage(image, 0, 0, dimension.width, dimension.height, this);
   
       }
   
   
       // Gets the image mime type. Supported formats are image/jpeg and image/png.
       private String getImageType(byte[] image) throws IOException{
   
           
           InputStream is = new BufferedInputStream(new ByteArrayInputStream(image));
           String mimeType = URLConnection.guessContentTypeFromStream(is);
   
           logger.log(Level.INFO, "Image type: {0}", mimeType);
   
           
           if (mimeType.equals("image/jpeg") || mimeType.equals("image/png")){
               return mimeType;
           }
           // not a supported file type.
           logger.log(Level.SEVERE, "Unsupported image type: {0}", mimeType);
           throw new IOException(String.format("Wrong image type. %s format isn't supported.",mimeType));
           }
   
       public static void main(String args[]) throws Exception {
   
           String photo = null;
           String projectName = null;
           String modelVersion = null;
   
           final String USAGE = "\n" +
           "Usage:\n" +
           "    DetectAnomalies <project> <version> <image> \n\n" +
           "Where:\n" +
           "    project - the Lookout for Vision project.\n\n"+
           "    version - the version of the model within the project.\n\n" +
           "    image - the path and filename of a local image. \n\n";
   
   
           try {
   
               if (args.length != 3) {
                   System.out.println(USAGE);
                   System.exit(1);
               }
   
               projectName = args[0];
               modelVersion = args[1];   
               photo = args[2]; 
               DetectAnomalies panel = null;
   
   
               // Get the Lookout for Vision client
               LookoutVisionClient lfvClient = LookoutVisionClient.builder().build();
   
               // Create frame and panel.
               JFrame frame = new JFrame("Anomaly Detection");
               frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
   
               panel = new DetectAnomalies(lfvClient, projectName, modelVersion, photo);
   
               frame.setContentPane(panel);
               frame.pack();
               frame.setVisible(true);
   
           } catch (LookoutVisionException lfvError) {
               logger.log(Level.SEVERE, "lookout for vision client error: {0}", lfvError.getMessage());
               System.out.println(String.format("lookout for vision client error: %s", lfvError.getMessage()));
               System.exit(1);
           } 
           catch(FileNotFoundException fileError)
           {
               logger.log(Level.SEVERE, "Could not find file: {0}", fileError.getMessage());
               System.out.println(String.format("Could not find file: %s", fileError.getMessage()));
               System.exit(1); 
           }
           catch(IOException ioError)
           {
               logger.log(Level.SEVERE, "IO error {0}", ioError.getMessage());
               System.out.println(String.format("IO error: %s", ioError.getMessage()));
               System.exit(1); 
           }
           
           
       }
   }
   ```

------

1. If you aren't planning to continue using your model, [stop your model](run-stop-model.md)\. 