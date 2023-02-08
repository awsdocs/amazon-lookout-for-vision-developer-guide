# Creating a dataset with a manifest file \(SDK\)<a name="create-dataset-sdk"></a>

You use the [CreateDataset](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_CreateDataset) operation to create the datasets associated with an Amazon Lookout for Vision project\. 

If you want to use a single dataset for training and testing, create a single dataset with the `DatasetType` value set to `train`\. During training, the dataset is internally split to make a training and test dataset\. You don't have access to the split training and test datasets\. If you want a separate test dataset, make a second call to `CreateDataset` with the `DatasetType` value set `test`\. During training, the training and test datasets are used to train and test the model\. 

You can optionally use the `DatasetSource` parameter to specify the location of a SageMaker Ground Truth format manifest file that's used to populate the dataset\. In this case, the call to `CreateDataset` is asynchronous\. To check the current status, call `DescribeDataset`\. For more information, see [Viewing your datasets](view-datasets.md)\. If a validation error occurs during import, the value of `Status` is set to CREATE\_FAILED and the status message \(`StatusMessage`\) is set\.  

**Tip**  
If you are creating a dataset with the [getting started](getting-started.md) example dataset, use the manifest file \(`getting-started/dataset-files/manifests/train.manifest`\) that the script creates in [Step 1: Create the manifest file and upload images](getting-started.md#getting-started-prepare-files)\.   
If you are creating a dataset with the [circuitboard](example-datasets.md#example-datasets-classification) example images, you have two options:  
Create the manifest file using code\. The [Amazon Lookout for Vision Lab](https://github.com/aws-samples/amazon-lookout-for-vision/blob/main/Amazon%20Lookout%20for%20Vision%20Lab.ipynb) Python Notebook shows how to create the manifest file for the circuitboard example images\. Alternatively, use the [Datasets example code](https://github.com/awsdocs/aws-doc-sdk-examples/blob/master/python/example_code/lookoutvision/datasets.py) in the AWS Code Examples Repository\.
If you've already used the Amazon Lookout for Vision console to create a dataset with the circuitboard example images, reuse the manifest files created for you by Amazon Lookout for Vision\. The training and test manifest file locations are `s3://bucket/datasets/project name/train or test/manifests/output/output.manifest`\. 

If you don't specify `DatasetSource`, an empty dataset is created\. In this case, the call to `CreateDataset` is synchronous\. Later, you can labeled images to the dataset by calling [UpdateDatasetEntries](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_UpdateDatasetEntries)\. For example code, see [Adding more images \(SDK\)](edit-dataset.md#add-more-images-dataset-sdk)\. 

If you want to replace a dataset, first delete the existing dataset with [DeleteDataset](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_DeleteDataset) and then create a new dataset of the same dataset type by calling `CreateDataset`\. For more information, see [Deleting a dataset](delete-dataset.md)\.

After you create the datasets, you can create the model\. For more information, see [Training a model \(SDK\)](model-train.md#create-model-sdk)\. 

You can view the labeled images \(JSON lines\) within a dataset by calling [ListDatasetEntries](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_ListDatasetEntries)\. You can add labeled images by calling `UpdateDatasetEntries`\. 

To view information about the test and training datasets, see [Viewing your datasets](view-datasets.md)\. 

**To create a dataset \(SDK\)**

1. If you haven't already done so, install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 4: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

1. Use the following example code to create a dataset\.

------
#### [ CLI ]

   Change the following values:
   + `project-name` to the name of the project that you want to associate the dataset with\.
   + `dataset-type` to the type of dataset that you want to create \(`train` or `test`\)\.
   + `dataset-source` to the Amazon S3 location of the manifest file\.
   + `Bucket` to the name of the Amazon S3 bucket that contains the manifest file\.
   + `Key` to the path and file name of the manifest file in the Amazon S3 bucket\.

   ```
   aws lookoutvision create-dataset --project-name project\
     --dataset-type train or test\
     --dataset-source '{ "GroundTruthManifest": { "S3Object": { "Bucket": "bucket", "Key": "manifest file" } } }' \
     --profile lookoutvision-access
   ```

------
#### [ Python ]

   This code is taken from the AWS Documentation SDK examples GitHub repository\. See the full example [here](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/python/example_code/lookoutvision/train_host.py)\. 

   ```
       @staticmethod
       def create_dataset(lookoutvision_client, project_name, manifest_file, dataset_type):
           """
           Creates a new Lookout for Vision dataset
   
           :param lookoutvision_client: A Lookout for Vision Boto3 client.
           :param project_name: The name of the project in which you want to
                                create a dataset.
           :param bucket: The bucket that contains the manifest file.
           :param manifest_file: The path and name of the manifest file.
           :param dataset_type: The type of the dataset (train or test).
           """
           try:
   
               bucket, key = manifest_file.replace("s3://", "").split("/", 1)
               logger.info("Creating %s dataset type...", dataset_type)
               dataset = {
                   "GroundTruthManifest": {"S3Object": {"Bucket": bucket, "Key": key}}
               }
               response = lookoutvision_client.create_dataset(
                   ProjectName=project_name,
                   DatasetType=dataset_type,
                   DatasetSource=dataset,
               )
               logger.info("Dataset Status: %s",
                           response["DatasetMetadata"]["Status"])
               logger.info(
                   "Dataset Status Message: %s",
                   response["DatasetMetadata"]["StatusMessage"],
               )
               logger.info("Dataset Type: %s",
                           response["DatasetMetadata"]["DatasetType"])
   
               # Wait until either created or failed.
               finished = False
               status = ""
               dataset_description = {}
               while finished is False:
                   dataset_description = lookoutvision_client.describe_dataset(
                       ProjectName=project_name, DatasetType=dataset_type
                   )
                   status = dataset_description["DatasetDescription"]["Status"]
   
                   if status == "CREATE_IN_PROGRESS":
                       logger.info("Dataset creation in progress...")
                       time.sleep(2)
                   elif status == "CREATE_COMPLETE":
                       logger.info("Dataset created.")
                       finished = True
                   else:
                       logger.info(
                           "Dataset creation failed: %s",
                           dataset_description["DatasetDescription"]["StatusMessage"])
                       finished = True
   
               if status != "CREATE_COMPLETE":
                   message = dataset_description["DatasetDescription"]["StatusMessage"]
                   logger.exception("Couldn't create dataset: %s", message)
                   raise Exception(f"Couldn't create dataset: {message}")
   
           except ClientError:
               logger.exception("Service error: Couldn't create dataset.")
               raise
   ```

------
#### [ Java V2 ]

   This code is taken from the AWS Documentation SDK examples GitHub repository\. See the full example [here](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/javav2/example_code/lookoutvision/src/main/java/com/example/lookoutvision/CreateDataset.java)\. 

   ```
   /**
    * Creates an Amazon Lookout for Vision dataset from a manifest file.
    * Returns after Lookout for Vision creates the dataset.
    * 
    * @param lfvClient    An Amazon Lookout for Vision client.
    * @param projectName  The name of the project in which you want to create a
    *                     dataset.
    * @param datasetType  The type of dataset that you want to create (train or
    *                     test).
    * @param bucket       The S3 bucket that contains the manifest file.
    * @param manifestFile The name and location of the manifest file within the S3
    *                     bucket.
    * @return DatasetDescription The description of the created dataset.
    */
   public static DatasetDescription createDataset(LookoutVisionClient lfvClient,
                   String projectName,
                   String datasetType,
                   String bucket,
                   String manifestFile)
                   throws LookoutVisionException, InterruptedException {
   
           logger.log(Level.INFO, "Creating {0} dataset for project {1}",
                           new Object[] { projectName, datasetType });
   
           // Build the request. If no bucket supplied, setup for empty dataset creation.
           CreateDatasetRequest createDatasetRequest = null;
   
           if (bucket != null && manifestFile != null) {
   
                   InputS3Object s3Object = InputS3Object.builder()
                                   .bucket(bucket)
                                   .key(manifestFile)
                                   .build();
   
                   DatasetGroundTruthManifest groundTruthManifest = DatasetGroundTruthManifest.builder()
                                   .s3Object(s3Object)
                                   .build();
   
                   DatasetSource datasetSource = DatasetSource.builder()
                                   .groundTruthManifest(groundTruthManifest)
                                   .build();
   
                   createDatasetRequest = CreateDatasetRequest.builder()
                                   .projectName(projectName)
                                   .datasetType(datasetType)
                                   .datasetSource(datasetSource)
                                   .build();
           } else {
                   createDatasetRequest = CreateDatasetRequest.builder()
                                   .projectName(projectName)
                                   .datasetType(datasetType)
                                   .build();
           }
   
           lfvClient.createDataset(createDatasetRequest);
   
           DatasetDescription datasetDescription = null;
   
           boolean finished = false;
   
           // Wait until dataset is created, or failure occurs.
           while (!finished) {
   
                   datasetDescription = describeDataset(lfvClient, projectName, datasetType);
   
                   switch (datasetDescription.status()) {
                           case CREATE_COMPLETE:
                                   logger.log(Level.INFO, "{0}dataset created for project {1}",
                                                   new Object[] { datasetType, projectName });
                                   finished = true;
                                   break;
                           case CREATE_IN_PROGRESS:
                                   logger.log(Level.INFO, "{0} dataset creating for project {1}",
                                                   new Object[] { datasetType, projectName });
   
                                   TimeUnit.SECONDS.sleep(5);
   
                                   break;
   
                           case CREATE_FAILED:
                                   logger.log(Level.SEVERE,
                                                   "{0} dataset creation failed for project {1}. Error {2}",
                                                   new Object[] { datasetType, projectName,
                                                                   datasetDescription.statusAsString() });
                                   finished = true;
                                   break;
                           default:
                                   logger.log(Level.SEVERE, "{0} error when creating {1} dataset for project {2}",
                                                   new Object[] { datasetType, projectName,
                                                                   datasetDescription.statusAsString() });
                                   finished = true;
                                   break;
   
                   }
           }
   
           logger.log(Level.INFO, "Dataset info. Status: {0}\n Message: {1} }",
                           new Object[] { datasetDescription.statusAsString(),
                                           datasetDescription.statusMessage() });
   
           return datasetDescription;
   
   }
   ```

------

1. Train your model by following the steps at [Training a model \(SDK\)](model-train.md#create-model-sdk)\.