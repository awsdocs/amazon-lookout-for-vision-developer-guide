# Showing classification and segmentation information<a name="inference-display-information"></a>

This example shows the analyzed image and overlays the analysis results\. If the response includes an anomaly mask, the mask is shown in the colors of the associated anomaly types\.

**To show image classification and image segmentation information**

1. If you haven't already done so, do the following:

   1. If you haven't already done so, install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 4: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

   1. [Train your model](model.md)\.

   1. [Start your model](run-start-model.md)\.

1. Make sure the user calling `DetectAnomalies` has access to the model version that you want to use\. For more information, see [Set up SDK permissions](su-sdk-permissions.md)\.

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
   Shows how to detect and show anomalies in an image using a trained Amazon Lookout
   for Vision model. The script displays the analysed image and overlays mask and analysis
   output.
   """
   
   import argparse
   import logging
   import io
   import boto3
   from PIL import Image, ImageDraw, ImageFont
   
   from botocore.exceptions import ClientError
   
   logger = logging.getLogger(__name__)
   
   
   class ShowAnomalies:
       """
       Class to detect and show anomalies in an image analyzed by detect_anomalies.
       """
   
   
       @staticmethod
       def draw_line(draw, text, fnt,  y_coordinate, color):
           """
           Draws a line of text on the supplied drawing surface.
           :param draw: The surface on which to draw the text.
           :param text: The text to draw in the drawing surface.
           :param fnt: The font for the text.
           :param y_coordinate: The y position for the text.
           :param color: The color for the text.
           :returns The y coordinate for the next line of text.
           """
           text_width, text_height = draw.textsize(text, fnt)
           draw.rectangle([(10, y_coordinate), (text_width + 10,
                                                y_coordinate + text_height)], fill="black")
           draw.text((10, y_coordinate), text, fill=color, font=fnt)
   
           y_coordinate += text_height
   
           return y_coordinate
   
       @staticmethod
       def draw_analysis_text(image, analysis):
           """
           Draws classification and segmentation info onto supplied image
           overlay analysis results on an image analyzed by detect_anomalies.
           :param analysis: The response from a call to detect_anomalies.
           :returns Nothing
           """
   
           ## Calculate a reasonable font size based on image width.
           font_size = int(image.size[0]/32)
   
           fnt = ImageFont.truetype('/Library/Fonts/Tahoma.ttf', font_size)
   
           draw = ImageDraw.Draw(image)
   
           y_coordinate = 0
   
           # Draw classification information.
           prediction = "Anomalous" if analysis["DetectAnomalyResult"]["IsAnomalous"] \
               else "Normal"
   
           confidence = analysis["DetectAnomalyResult"]["Confidence"]
           found_anomalies = analysis["DetectAnomalyResult"]['Anomalies']
           segmentation_info = False
   
           logger.info("Prediction: %s", format(prediction))
           logger.info("Confidence: %s", format(confidence))
   
           y_coordinate = 0
           y_coordinate = ShowAnomalies.draw_line(
               draw, "Classification", fnt, y_coordinate, "white")
           y_coordinate = ShowAnomalies.draw_line(
               draw, f"  Prediction: {prediction}", fnt, y_coordinate, "white")
           y_coordinate = ShowAnomalies.draw_line(
               draw, f"  Confidence: {confidence:.2%}", fnt, y_coordinate, "white")
   
           # Draw segmentation information, if present.
           if (len(found_anomalies)) > 1:
               logger.info("Anomalies:")
   
               y_coordinate = ShowAnomalies.draw_line(
                   draw, "Segmentation:", fnt, y_coordinate, "white")
               for i in range(1, len(found_anomalies)):
   
                   # Only display info if more than 0% coverage found.
                   percent_coverage = found_anomalies[i]['PixelAnomaly']['TotalPercentageArea']
                   if percent_coverage > 0:
                       segmentation_info = True
                       logger.info("  %s", found_anomalies[i]['Name'])
                       logger.info("    Color: %s",
                                   found_anomalies[i]['PixelAnomaly']['Color'])
                       logger.info("    Area: %s", percent_coverage)
                       y_coordinate = ShowAnomalies.draw_line(
                           draw,
                           f"  Anomaly: {found_anomalies[i]['Name']}. Area: {percent_coverage:.2%}",
                           fnt,
                           y_coordinate,
                           found_anomalies[i]['PixelAnomaly']['Color'])
   
               if not segmentation_info:
                   y_coordinate = ShowAnomalies.draw_line(
                       draw, "No segmentation information found.", fnt, y_coordinate, "white")
   
   
   
   
       @staticmethod
       def show_anomaly_prediction(lookoutvision_client, project_name, model_version, photo):
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
   
               logger.info("Detecting anomalies in %s", photo)
   
               image = Image.open(photo)
               image_type = Image.MIME[image.format]
   
               # Check that image type is valid.
               if image_type not in ("image/jpeg", "image/png"):
                   logger.info("Invalid image type for %s", photo)
                   raise ValueError(
                       f"Invalid file format. Supply a jpeg or png format file: {photo}"
                   )
   
               # Get images bytes for call to detect_anomalies.
               image_bytes = io.BytesIO()
               image.save(image_bytes, format=image.format)
               image_bytes = image_bytes.getvalue()
   
               # Analyze the image.
               response = lookoutvision_client.detect_anomalies(
                   ProjectName=project_name,
                   ContentType=image_type,
                   Body=image_bytes,
                   ModelVersion=model_version
               )
   
               # Overlay mask onto analyzed image.
               image_mask_bytes = response["DetectAnomalyResult"]["AnomalyMask"]
               image_mask = Image.open(io.BytesIO(image_mask_bytes))
   
               final_img = Image.blend(image, image_mask, 0.5) \
                   if response["DetectAnomalyResult"]["IsAnomalous"] else image
   
               # Overlay analysis output on image.
               ShowAnomalies.draw_analysis_text(final_img, response)
   
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
           logging.basicConfig(level=logging.INFO,
                               format="%(levelname)s: %(message)s")
   
           session = boto3.Session(
               profile_name='lookoutvision-access')
   
           lookoutvision_client = session.client("lookoutvision")
   
           parser = argparse.ArgumentParser(usage=argparse.SUPPRESS)
   
           add_arguments(parser)
   
           args = parser.parse_args()
   
           # Analyze the image and show results.
           ShowAnomalies.show_anomaly_prediction(
               lookoutvision_client, args.project, args.version, args.image
           )
   
       except ClientError as err:
           print("A service error occured: " +
                 format(err.response["Error"]["Message"]))
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

   The following example code detects anomalies in an image that you supply\. The example takes the following command line options: 
   + `project` – the name of the project that you want to use\. 
   + `version` – the version of the model, within the project, that you want to use\.
   + `image` – the path and file of a local image file \(JPEG or PNG format\)\. 

   ```
   /*
      Copyright Amazon.com, Inc. or its affiliates. All Rights Reserved.
      SPDX-License-Identifier: Apache-2.0
   */
   
   
   package com.example.lookoutvision;
   
   import software.amazon.awssdk.auth.credentials.ProfileCredentialsProvider;
   import software.amazon.awssdk.core.SdkBytes;
   import software.amazon.awssdk.core.sync.RequestBody;
   import software.amazon.awssdk.services.lookoutvision.LookoutVisionClient;
   import software.amazon.awssdk.services.lookoutvision.model.Anomaly;
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
   
   // Finds anomalies on a supplied image.
   public class ShowAnomalies extends JPanel {
   /**
    * Finds and displays anomalies on a supplied image.
    */
   
       private static final long serialVersionUID = 1L;
       private transient BufferedImage image;
       private transient BufferedImage maskImage;
       private transient Dimension dimension;
       public static final Logger logger = Logger.getLogger(ShowAnomalies.class.getName());
   
       // Constructor. Finds anomalies in a local image file.
       public ShowAnomalies(LookoutVisionClient lfvClient, String projectName, String modelVersion,
               String photo) throws IOException, LookoutVisionException {
   
           logger.log(Level.INFO, "Processing local file: {0}", photo);
   
           maskImage = null;
   
           // Get image bytes and buffered image.
           InputStream sourceStream = new FileInputStream(new File(photo));
           SdkBytes imageSDKBytes = SdkBytes.fromInputStream(sourceStream);
           byte[] imageBytes = imageSDKBytes.asByteArray();
           ByteArrayInputStream inputStream = new ByteArrayInputStream(imageSDKBytes.asByteArray());
           image = ImageIO.read(inputStream);
   
           // Get the image type. Can be image/jpeg or image/png.
           String contentType = getImageType(imageBytes);
   
           // Set the size of the window that shows the image.
           setWindowDimensions();
   
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
   
           
           if (result.anomalyMask() != null){
               SdkBytes maskSDKBytes = result.anomalyMask();
   
               ByteArrayInputStream maskInputStream = new ByteArrayInputStream(maskSDKBytes.asByteArray());
               maskImage = ImageIO.read(maskInputStream);
           }
   
           drawImageInfo(result);
   
       }
   
       // Sets window dimensions to 1/2 screen size, unless image is smaller.
       public void setWindowDimensions() {
           dimension = java.awt.Toolkit.getDefaultToolkit().getScreenSize();
   
           dimension.width = (int) dimension.getWidth() / 2;
           dimension.height = (int) dimension.getHeight() / 2;
   
           if (image.getWidth() < dimension.width || image.getHeight() < dimension.height) {
               dimension.width = image.getWidth();
               dimension.height = image.getHeight();
           }
           setPreferredSize(dimension);
   
       }
   
       private int drawLine(Graphics2D g2d, String line, FontMetrics metrics, int yPos, Color color) {
       /**
       * Draws a line of text at the spsecified y position and color.
       * confidence
       * 
       * @param g2D The Graphics2D object for the image.
       * @param line The line of text to draw.
       * @param metrics The font information to use.
       * @param yPos The y position for the line of text.
       * 
       * @return  The yPos for the next line of text.
       */
   
           int indent = 10;
   
           // Get text height, width, and descent.
           int textWidth = metrics.stringWidth(line);
           LineMetrics lm = metrics.getLineMetrics(line, g2d);
           int textHeight = (int) lm.getHeight();
           int descent = (int) lm.getDescent();
   
           int y2Pos = (yPos + textHeight) - descent;
   
           // Draw black rectangle.
           g2d.setColor(Color.BLACK);
           g2d.fillRect(indent, yPos, textWidth, textHeight);
   
           // Draw text.
           g2d.setColor(color);
           g2d.drawString(line, indent, y2Pos);
   
           yPos += textHeight;
   
           return yPos;
   
       }
   
       public void drawImageInfo(DetectAnomalyResult result) {
       /** 
        * Draws the results from DetectAnomalies onto the output image.
        * 
        * @param result The response from a call to
        *               DetectAnomalies.
        * 
        */
   
           // Set up drawing.
           Graphics2D g2d = image.createGraphics();
           
           if (result.anomalyMask() != null){
               Composite composite = g2d.getComposite();
               g2d.setComposite(AlphaComposite.SrcOver.derive(0.5f));
               int x = (image.getWidth() - maskImage.getWidth()) / 2;
               int y = (image.getHeight() - maskImage.getHeight()) / 2;
               g2d.drawImage(maskImage, x, y, null);
               // Set composite for overlaying text.
               g2d.setComposite(composite);
           }
   
           //Calculate font size based on arbitary 32 pixel image width.
           int fontSize = (image.getWidth() / 32);
         
   
           g2d.setFont(new Font("Tahoma", Font.PLAIN, fontSize));
           Font font = g2d.getFont();
           FontMetrics metrics = g2d.getFontMetrics(font);
   
           // Get classification information.
   
           String prediction = "Prediction: Normal";
   
           if (Boolean.TRUE.equals(result.isAnomalous())) {
               prediction = "Prediction: Anomalous";
           }
   
           // Convert prediction to percentage.
           NumberFormat defaultFormat = NumberFormat.getPercentInstance();
           defaultFormat.setMinimumFractionDigits(1);
           String confidence = String.format("Confidence: %s", defaultFormat.format(result.confidence()));
   
           // Draw classification information.
           int yPos = 0;
   
           yPos = drawLine(g2d, "Classification:", metrics, yPos, Color.WHITE);
           yPos = drawLine(g2d, prediction, metrics, yPos, Color.WHITE);
           yPos = drawLine(g2d, confidence, metrics, yPos, Color.WHITE);
   
           // Draw segmentation info.
           yPos = drawLine(g2d, "Segmentation:", metrics, yPos, Color.WHITE);
   
           // Ignore background label, so size must be > 1
           if (result.anomalies().size() > 1) {
               for (Anomaly anomaly : result.anomalies()) {
                   if (anomaly.name().equals("background"))
                       continue;
                   String label = String.format("Anomaly: %s. Area: %s", anomaly.name(),
                           defaultFormat.format(anomaly.pixelAnomaly().totalPercentageArea()));
                   Color anomalyColor = Color.decode((anomaly.pixelAnomaly().color()));
                   yPos = drawLine(g2d, label, metrics, yPos, anomalyColor);
   
               }
   
           } else {
               drawLine(g2d, "None found.", metrics, yPos, Color.WHITE);
   
           }
   
           g2d.dispose();
   
       }
   
       @Override
       public void paintComponent(Graphics g)
       /**
        * Draws the image and analysis results.
        * 
        * @param g The Graphics context object for drawing.
        *          DetectAnomalies.
        * 
        */
       {
   
           Graphics2D g2d = (Graphics2D) g; // Create a Java2D version of g.
   
           // Draw the image.
           g2d.drawImage(image, 0, 0, dimension.width, dimension.height, this);
   
       }
   
       // Gets the image mime type. Supported formats are image/jpeg and image/png.
   
       private String getImageType(byte[] image) throws IOException
       /**
        * Gets the file type of a supplied image. Raises an exception if the image
        * isn't compatible with with Amazon Lookout for Vision.
        * 
        * @param image The image that you want to check.
        * 
        * @return String The type of the image.
        */
   
       {
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
   
       public static void main(String[] args) throws Exception {
   
           String photo = null;
           String projectName = null;
           String modelVersion = null;
   
           final String USAGE = "\n" +
                   "Usage:\n" +
                   "    DetectAnomalies <project> <version> <image> \n\n" +
                   "Where:\n" +
                   "    project - The Lookout for Vision project.\n\n" +
                   "    version - The version of the model within the project.\n\n" +
                   "    image - The path and filename of a local image. \n\n";
   
           try {
   
               if (args.length != 3) {
                   System.out.println(USAGE);
                   System.exit(1);
               }
   
               projectName = args[0];
               modelVersion = args[1];
               photo = args[2];
               ShowAnomalies panel = null;
   
               // Get the Lookout for Vision client.
               LookoutVisionClient lfvClient = LookoutVisionClient.builder()
                   .credentialsProvider(ProfileCredentialsProvider.create("lookoutvision-access"))
                   .build();
   
               // Create frame and panel.
               JFrame frame = new JFrame(photo);
               frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
   
               panel = new ShowAnomalies(lfvClient, projectName, modelVersion, photo);
   
               frame.setContentPane(panel);
               frame.pack();
               frame.setVisible(true);
   
   
           } catch (LookoutVisionException lfvError) {
               logger.log(Level.SEVERE, "Lookout for Vision client error: {0}: {1}",
                       new Object[] { lfvError.awsErrorDetails().errorCode(),
                               lfvError.awsErrorDetails().errorMessage() });
               System.out.println(String.format("lookout for vision client error: %s", lfvError.getMessage()));
               System.exit(1);
   
           } catch (FileNotFoundException fileError) {
               logger.log(Level.SEVERE, "Could not find file: {0}", fileError.getMessage());
               System.out.println(String.format("Could not find file: %s", fileError.getMessage()));
               System.exit(1);
   
           } catch (IOException ioError) {
               logger.log(Level.SEVERE, "IO error {0}", ioError.getMessage());
               System.out.println(String.format("IO error: %s", ioError.getMessage()));
               System.exit(1);
           }
   
       }
   }
   ```

------

1. If you aren't planning to continue using your model, [stop your model](run-stop-model.md)\. 