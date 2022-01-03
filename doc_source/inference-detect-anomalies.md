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

The Python and Java examples display the image and overlay the analysis results, as shown in the following image\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/lookout-for-vision/latest/developer-guide/images/prediction.jpg)

**To detect anomalies in an image \(API\)**

1. If you haven't already done so, do the following:

   1. Create or update an IAM user\.  For more information, see [Step 2: Create an IAM administrator user and group](su-account-user.md)\.

   1. Install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 5: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

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
               if (response["DetectAnomalyResult"]["IsAnomalous"]==False):
                       prediction="Abnormal"
   
               confidence=response["DetectAnomalyResult"]["Confidence"]
   
               logger.info("Prediction: %s", format(prediction))
               logger.info("Confidence: %s", format(confidence))
   
               draw = ImageDraw.Draw(image)  
               
               font_size=100
   
               fnt = ImageFont.truetype('/Library/Fonts/Tahoma.ttf', font_size)
               y1=0
               
               # Draw black box and overlay text
               for text in [f"File: {photo}", f"Prediction: {prediction}", f"Confidence: {confidence:.2%}"]:
                   
                   text_width, text_height = draw.textsize(text,fnt)
   
                   y2 = y1 + text_height 
                 
                   draw.rectangle([(10 , y1), (text_width + 10, y2)],fill="black")
                   draw.text((10,y1), text, fill="lime", font=fnt)  
                   
                   y1+=text_height
   
               
               image.show()
       
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
       main().
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