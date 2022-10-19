# Adding images to your dataset<a name="edit-dataset"></a>

After you create a dataset, you might want to add more images to the dataset\. For example, if model evaluation indicates a poor model, you can enhance the quality of your model by adding more images\. If you have created a test dataset, adding more images can increase the accuracy of your model's performance metrics\.

Retrain your model after updating your datasets\.

**Topics**
+ [Adding more images](#add-more-images-dataset)
+ [Adding more images \(SDK\)](#add-more-images-dataset-sdk)

## Adding more images<a name="add-more-images-dataset"></a>

You can add more images to your datasets by uploading images from your local computer\. To add more labeled images with the SDK, use the [UpdateDatasetEntries](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_UpdateDatasetEntries) operation\.

**To add more images to your dataset \(console\)**

1. Choose **Actions** and select the dataset that you want to add images to\. 

1. Choose the images you want to upload to the dataset\. You can drag the images or choose the images that you want to upload from your local computer\. You can upload up to 30 images at a time\.

1. Choose **Upload images**\.

1. Choose **Save changes**\.

When you are done adding more images, you need to label them so that they can be used to train the model\. For more information, see [Classifying images \(console\)](model-label.md)\.

## Adding more images \(SDK\)<a name="add-more-images-dataset-sdk"></a>

To add more labeled images with the SDK, use the [UpdateDatasetEntries](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_UpdateDatasetEntries) operation\. You supply a manifest file that contains the images that you want to add\. You can also update existing images by specifying the image in the `source-ref` field of the JSON line in the manifest file\. For more information, see [Creating a manifest file](manifest-files.md)\.

**To add more images to a dataset \(SDK\)**

1. If you haven't already done so, do the following:

   1. Create or update an IAM user with permissions to access Amazon Lookout for Vision\. For more information, see [Step 3: Set up permissions](su-setup-permissions.md)\. 

   1. Install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 5: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

1. Use the following example code to add more images to a dataset\.

------
#### [ CLI ]

   Change the following values:
   + `project-name` to the name of the project that contains the dataset you want to update\. 
   + `dataset-type` to the type of dataset that you want to update \(`train` or `test`\)\.
   + `changes` to the location the manifest file that contain dataset updates\. 

   ```
   aws lookoutvision update-dataset-entries\
       --project-name project\
       --dataset-type train or test\
       --changes fileb://manifest file
   ```

------
#### [ Python ]

   This code is taken from the AWS Documentation SDK examples GitHub repository\. See the full example [here](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/python/example_code/lookoutvision/train_host.py)\. 

   ```
       @staticmethod
       def update_dataset_entries(lookoutvision_client, project_name, dataset_type, updates_file):
           """
           Adds dataset entries to an Amazon Lookout for Vision dataset.    
           :param lookoutvision_client: The Amazon Rekognition Custom Labels Boto3 client.
           :param project_name: The project that contains the dataset that you want to update.
           :param dataset_type: The type of the dataset that you want to update (train or test).
           :param updates_file: The manifest file of JSON Lines that contains the updates. 
           """
   
           try:
               status = ""
               status_message = ""
               manifest_file = ""
   
               # Update dataset entries
               logger.info(f"""Updating {dataset_type} dataset for project {project_name}
   with entries from {updates_file}.""")
   
               with open(updates_file) as f:
                   manifest_file = f.read()
   
               lookoutvision_client.update_dataset_entries(
                   ProjectName=project_name,
                   DatasetType=dataset_type,
                   Changes=manifest_file,
               )
   
               finished = False
               while finished == False:
   
                   dataset = lookoutvision_client.describe_dataset(ProjectName=project_name,
                                                                   DatasetType=dataset_type)
   
                   status = dataset['DatasetDescription']['Status']
                   status_message = dataset['DatasetDescription']['StatusMessage']
   
                   if status == "UPDATE_IN_PROGRESS":
                       logger.info(
                           (f"Updating {dataset_type} dataset for project {project_name}."))
                       time.sleep(5)
                       continue
   
                   if status == "UPDATE_FAILED_ROLLBACK_IN_PROGRESS":
                       logger.info(
                           (f"Update failed, rolling back {dataset_type} dataset for project {project_name}."))
                       time.sleep(5)
                       continue
   
                   if status == "UPDATE_COMPLETE":
                       logger.info(
                           f"Dataset updated: {status} : {status_message} : {dataset_type} dataset for project {project_name}.")
                       finished = True
                       continue
   
                   if status == "UPDATE_FAILED_ROLLBACK_COMPLETE":
                       logger.info(
                           f"Rollback complated after update failure: {status} : {status_message} : {dataset_type} dataset for project {project_name}.")
                       finished = True
                       continue
   
                   logger.exception(
                       f"Failed. Unexpected state for dataset update: {status} : {status_message} : {dataset_type} dataset for project {project_name}.")
                   raise Exception(
                       f"Failed. Unexpected state for dataset update: {status} : {status_message} :{dataset_type} dataset for project {project_name}.")
   
               logger.info(f"Added entries to dataset.")
   
               return status, status_message
   
           except ClientError as err:
               logger.exception(
                   f"Couldn't update dataset: {err.response['Error']['Message']}")
               raise
   ```

------
#### [ Java V2 ]

   This code is taken from the AWS Documentation SDK examples GitHub repository\. See the full example [here](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/javav2/example_code/lookoutvision/src/main/java/com/example/lookoutvision/UpdateDatasetEntries.java)\. 

   ```
   /**
    * Updates an Amazon Lookout for Vision dataset from a manifest file.
    * Returns after Lookout for Vision updates the dataset.
    * 
    * @param lfvClient    An Amazon Lookout for Vision client.
    * @param projectName  The name of the project in which you want to update a
    *                     dataset.
    * @param datasetType  The type of the dataset that you want to update (train or
    *                     test).
    * @param manifestFile The name and location of a local manifest file that you want to
    * use to update the dataset.
    * @return DatasetStatus The status of the updated dataset.
    */
   
   public static DatasetStatus updateDatasetEntries(LookoutVisionClient lfvClient, String projectName,
                   String datasetType, String updateFile) throws FileNotFoundException, LookoutVisionException,
                   InterruptedException {
   
           logger.log(Level.INFO, "Updating {0} dataset for project {1}",
                           new Object[] { datasetType, projectName });
   
           InputStream sourceStream = new FileInputStream(updateFile);
           SdkBytes sourceBytes = SdkBytes.fromInputStream(sourceStream);
   
           UpdateDatasetEntriesRequest updateDatasetEntriesRequest = UpdateDatasetEntriesRequest.builder()
                           .projectName(projectName)
                           .datasetType(datasetType)
                           .changes(sourceBytes)
                           .build();
   
           lfvClient.updateDatasetEntries(updateDatasetEntriesRequest);
   
           boolean finished = false;
           DatasetStatus status = null;
   
           // Wait until update completes.
   
           do {
   
                   DescribeDatasetRequest describeDatasetRequest = DescribeDatasetRequest.builder()
                                   .projectName(projectName)
                                   .datasetType(datasetType)
                                   .build();
                   DescribeDatasetResponse describeDatasetResponse = lfvClient
                                   .describeDataset(describeDatasetRequest);
   
                   DatasetDescription datasetDescription = describeDatasetResponse.datasetDescription();
   
                   status = datasetDescription.status();
   
                   switch (status) {
   
                           case UPDATE_COMPLETE:
                                   logger.log(Level.INFO, "{0} Dataset updated for project {1}.",
                                                   new Object[] { datasetType, projectName });
                                   finished = true;
                                   break;
   
                           case UPDATE_IN_PROGRESS:
   
                                   logger.log(Level.INFO, "{0} Dataset update for project {1} in progress.",
                                                   new Object[] { datasetType, projectName });
                                   TimeUnit.SECONDS.sleep(5);
   
                                   break;
   
                           case UPDATE_FAILED_ROLLBACK_IN_PROGRESS:
                                   logger.log(Level.SEVERE,
                                                   "{0} Dataset update failed for project {1}. Rolling back",
                                                   new Object[] { datasetType, projectName });
   
                                   TimeUnit.SECONDS.sleep(5);
   
                                   break;
   
                           case UPDATE_FAILED_ROLLBACK_COMPLETE:
                                   logger.log(Level.SEVERE,
                                                   "{0} Dataset update failed for project {1}. Rollback completed.",
                                                   new Object[] { datasetType, projectName });
                                   finished = true;
                                   break;
   
                           default:
                                   logger.log(Level.SEVERE,
                                                   "{0} Dataset update failed for project {1}. Unexpected error returned.",
                                                   new Object[] { datasetType, projectName });
                                   finished = true;
   
                   }
   
           } while (!finished);
   
           return status;
   
   }
   ```

------

1. Repeat the previous step and provide values for the other dataset type\.