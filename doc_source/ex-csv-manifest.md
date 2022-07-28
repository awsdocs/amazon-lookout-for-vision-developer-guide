# Creating a classification manifest file from a CSV file<a name="ex-csv-manifest"></a>

This example Python script simplifies the creation of a classification manifest file by using a Comma Separated Values \(CSV\) file to label images\. You create the CSV file\. 

A manifest file describes the images used to train a model\. A manifest file is made up of one or more JSON lines\. Each JSON line describes a single image\. For more information, see [Defining JSON lines for image classification](manifest-file-classification.md)\. 

A CSV file represents tabular data over multiple rows in a text file\. Fields on a row are separated by commas\. For more information, see [comma separated values](https://en.wikipedia.org/wiki/Comma-separated_values)\. For this script, each row in your CSV file includes the S3 location of an image and the anomaly classification for the image \(`normal` or `anomaly`\)\. Each row maps to a JSON Line in the manifest file\.

For example, The following CSV file describes some of the images in the [example images](su-prepare-example-images.md)\. 

```
s3://s3bucket/circuitboard/train/anomaly/train-anomaly_1.jpg,anomaly
s3://s3bucket/circuitboard/train/anomaly/train-anomaly_10.jpg,anomaly
s3://s3bucket/circuitboard/train/anomaly/train-anomaly_11.jpg,anomaly
s3://s3bucket/circuitboard/train/normal/train-normal_1.jpg,normal
s3://s3bucket/circuitboard/train/normal/train-normal_10.jpg,normal
s3://s3bucket/circuitboard/train/normal/train-normal_11.jpg,normal
```

The script generates JSON Lines for each row\. For example, the following is the JSON Line for the first row \(`s3://s3bucket/circuitboard/train/anomaly/train-anomaly_1.jpg,anomaly`\) \.

```
{"source-ref": "s3://s3bucket/csv_test/train_anomaly_1.jpg","anomaly-label": 1,"anomaly-label-metadata": {"confidence": 1,"job-name": "labeling-job/anomaly-classification","class-name": "anomaly","human-annotated": "yes","creation-date": "2022-02-04T22:47:07","type": "groundtruth/image-classification"}}
```

If your CSV file doesn't include the Amazon S3 path for the images, use the `--s3-path` command line argument to specify the Amazon S3 path to the images\. 

Before creating the manifest file, the script checks for duplicate images in the CSV file and any image classifications that are not `normal` or `anomaly`\. If duplicates image or image classification errors are found, the script does the following:
+ Records the first valid image entry for all images in a deduplicated CSV file\. 
+ Records duplicate occurences of an image in the errors file\. 
+ Records image classifications that are not `normal` or `anomaly` in the errors file\.
+ Doesn't create a manifest file\.

The errors file includes the line number where a duplicate image or classification error is found in the input CSV file\. Use the errors CSV file to update the input CSV file and then run the script again\. Alternatively, use the errors CSV file to update the deduplicated CSV file, which only contains unique image entries and images with no image classification errors\. Rerun the script with the updated deduplicated CSV file\.

If no duplicates or errors are found in the input CSV file, the script deletes the deduplicated image CSV file and errors file, as they are empty\. 

 In this procedure, you create the CSV file and run the Python script to create the manifest file\. The script has been tested with Python version 3\.7\. 

**To create a manifest file from a CSV file**

1. Create a CSV file with the following fields in each row \(one row per image\)\. Don't add a header row to the CSV file\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/lookout-for-vision/latest/developer-guide/ex-csv-manifest.html)

   For example `s3://s3bucket/circuitboard/train/anomaly/image_10.jpg,anomaly` or `image_11.jpg,normal` 

1. Save the CSV file\.

1. Run the following Python script\. Supply the following arguments:
   + `csv_file` – The CSV file that you created in step 1\. 
   + \(Optional\)`--s3-path s3://path_to_folder/` – The Amazon S3 path to add to the image file names \(field 1\)\. Use `--s3-path` if the images in field 1 don't already contain an S3 path\.

   ```
   # Copyright Amazon.com, Inc. or its affiliates. All Rights Reserved.
   # SPDX-License-Identifier:  Apache-2.0
   """
   Purpose
   Shows how to create an Amazon Lookout for Vision manifest file from a CSV file.
   The CSV file format is image location,anomaly classification (normal or anomaly)
   For example:
   s3://s3bucket/circuitboard/train/anomaly/train_11.jpg,anomaly
   s3://s3bucket/circuitboard/train/normal/train_1.jpg,normal
   
   If necessary, use the bucket argument to specify the Amazon S3 bucket folder for the images.
   """
   
   from datetime import datetime, timezone
   import argparse
   import logging
   import csv
   import os
   import json
   
   logger = logging.getLogger(__name__)
   
   
   def check_errors(csv_file):
       """
       Checks for duplicate images and incorrect classifications in a CSV file.
       If duplicate images or invalid anomaly assignments are found, an errors CSV file
       and deduplicated CSV file are created. Only the first
       occurrence of a duplicate is recorded. Other duplicates are recorded in the errors file.
       :param csv_file: The source CSV file
       :return: True if errors or duplicates are found, otherwise false.
       """
   
       logger.info("Checking %s.", csv_file)
   
       errors_found = False
       errors_file = f"{os.path.splitext(csv_file)[0]}_errors.csv"
       deduplicated_file = f"{os.path.splitext(csv_file)[0]}_deduplicated.csv"
   
       with open(csv_file, 'r', encoding="UTF-8") as input_file,\
               open(deduplicated_file, 'w', encoding="UTF-8") as dedup,\
               open(errors_file, 'w', encoding="UTF-8") as errors:
   
           reader = csv.reader(input_file, delimiter=',')
           dedup_writer = csv.writer(dedup)
           error_writer = csv.writer(errors)
           line = 1
           entries = set()
           for row in reader:
   
               # Skip empty lines.
               if not ''.join(row).strip():
                   continue
   
               # Record any incorrect classifications.
               if not row[1].lower() == "normal" and not row[1].lower() == "anomaly":
                   error_writer.writerow(
                       [line, row[0], row[1], "INVALID_CLASSIFICATION"])
                   errors_found = True
   
               # Write first image entry to dedup file and record duplicates.
               key = row[0]
               if key not in entries:
                   dedup_writer.writerow(row)
                   entries.add(key)
               else:
                   error_writer.writerow([line, row[0], row[1], "DUPLICATE"])
                   errors_found = True
               line += 1
   
       if errors_found:
           logger.info("Errors found check %s.", errors_file)
       else:
           os.remove(errors_file)
           os.remove(deduplicated_file)
   
       return errors_found
   
   
   def create_manifest_file(csv_file, manifest_file, s3_path):
       """
       Read a CSV file and create an Amazon Lookout for Vision classification manifest file.
       :param csv_file: The source CSV file.
       :param manifest_file: The name of the manifest file to create.
       :param s3_path: The Amazon S3 path to the folder that contains the images.
       """
       logger.info("Processing CSV file %s.", csv_file)
   
       image_count = 0
       anomalous_count = 0
   
       with open(csv_file, newline='', encoding="UTF-8") as csvfile,\
           open(manifest_file, "w", encoding="UTF-8") as output_file:
   
           image_classifications = csv.reader(
               csvfile, delimiter=',', quotechar='|')
   
           # Process each row (image) in the CSV file.
           for row in image_classifications:
               # Skip empty lines.
               if not ''.join(row).strip():
                   continue
   
               source_ref = str(s3_path) + row[0]
               classification = 0
   
               if row[1].lower() == 'anomaly':
                   classification = 1
                   anomalous_count += 1
   
              # Create the JSON line.
               json_line = {}
               json_line['source-ref'] = source_ref
               json_line['anomaly-label'] = str(classification)
   
               metadata = {}
               metadata['confidence'] = 1
               metadata['job-name'] = "labeling-job/anomaly-classification"
               metadata['class-name'] = row[1]
               metadata['human-annotated'] = "yes"
               metadata['creation-date'] = datetime.now(timezone.utc).strftime('%Y-%m-%dT%H:%M:%S.%f')
               metadata['type'] = "groundtruth/image-classification"
   
               json_line['anomaly-label-metadata'] = metadata
   
               output_file.write(json.dumps(json_line))
               output_file.write('\n')
               image_count += 1
   
       logger.info("Finished creating manifest file %s.\n"
                   "Images: %s\nAnomalous: %s",
                   manifest_file,
                   image_count,
                   anomalous_count)
       return image_count, anomalous_count
   
   
   def add_arguments(parser):
       """
       Add command line arguments to the parser.
       :param parser: The command line parser.
       """
   
       parser.add_argument(
           "csv_file", help="The CSV file that you want to process."
       )
   
       parser.add_argument(
           "--s3_path", help="The Amazon S3 bucket and folder path for the images."
           " If not supplied, column 1 is assumed to include the Amazon S3 path.", required=False
       )
   
   
   def main():
   
       logging.basicConfig(level=logging.INFO,
                           format="%(levelname)s: %(message)s")
   
       try:
   
           # Get command line arguments.
           parser = argparse.ArgumentParser(usage=argparse.SUPPRESS)
           add_arguments(parser)
           args = parser.parse_args()
           s3_path = args.s3_path
           if s3_path is None:
               s3_path = ""
   
           csv_file = args.csv_file
           csv_file_no_extension = os.path.splitext(csv_file)[0]
           manifest_file = csv_file_no_extension + '.manifest'
   
           # Create manifest file if there are no duplicate images.
           if check_errors(csv_file):
               print(f"Issues found. Use {csv_file_no_extension}_errors.csv "\
                   "to view duplicates and errors.")
               print(f"{csv_file}_deduplicated.csv contains the first"\
                   "occurrence of a duplicate.\n"
                     "Update as necessary with the correct information.")
               print(f"Re-run the script with {csv_file_no_extension}_deduplicated.csv")
           else:
               print('No duplicates found. Creating manifest file.')
   
               image_count, anomalous_count = create_manifest_file(csv_file, manifest_file, s3_path)
   
               print(f"Finished creating manifest file: {manifest_file} \n")
   
               normal_count = image_count-anomalous_count
               print(f"Images processed: {image_count}")
               print(f"Normal: {normal_count}")
               print(f"Anomalous: {anomalous_count}")
   
       except FileNotFoundError as err:
           logger.exception("File not found.:%s", err)
           print(f"File not found: {err}. Check your input CSV file.")
   
   if __name__ == "__main__":
       main()
   ```

1. If duplicate images occur or classification errors occur: 

   1. Use the errors file to update the dedupulicated CSV file or the input CSV file\. 

   1. Run the script again with the updated deduplicated CSV file or updated input CSV file\.

1. If you plan to use a test dataset, repeat steps 1–4 to create a manifest file for your test dataset\.

1. If necessary, copy the images from your computer to the Amazon S3 bucket path that you specified in column 1 of the CSV file \(or specified in the `--s3-path` command line\)\. To copy the images, enter the following command at the command prompt\.

   ```
   aws s3 cp --recursive your-local-folder/ s3://your-target-S3-location/
   ```

1. Follow the instructions at [Creating a dataset with a manifest file \(console\)](create-dataset-use-manifest.md) to create a dataset\. If you are use the AWS SDK, see [Creating a dataset with a manifest file \(SDK\)](create-dataset-sdk.md)\. 