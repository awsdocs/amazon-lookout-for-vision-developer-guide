--------

Amazon Lookout for Vision is in preview release and is subject to change\.

--------

# Creating a dataset with a manifest file \(SDK\)<a name="create-dataset-sdk"></a>

You use the [CreateDataset](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/CreateDataset) operation to create the datasets associated with an Amazon Lookout for Vision project\. 

If you want to use a single dataset for training and testing, create a single dataset with the `DatasetType` value set to `train`\. During training, the dataset is internally split to make a training and test dataset\. You don't have access to the split training and test datasets\. If you want a separate test dataset, make a second call to `CreateDataset` with the `DatasetType` value set `test`\. During training, the training and test datasets are used to train and test the model\. 

You can optionally use the `DatasetSource` parameter to specify the location of a SageMaker Ground Truth format manifest file that's used to populate the dataset\. In this case, the call to `CreateDataset` is asynchronous\. To check the current status, call `DescribeDataset`\. For more information, see [Viewing your datasets](view-datasets.md)\. If a validation error occurs during import, the value of `Status` is set to CREATE\_FAILED and the status message \(`StatusMessage`\) is set\.  

If you don't specify `DatasetSource`, an empty dataset is created\. In this case, the call to `CreateDataset` is synchronous\. Later, you can labeled images to the dataset by calling `UpdateDatasetEntries`\.

If you want to replace a dataset, first delete the existing dataset with `DeleteDataset` and then create a new dataset of the same dataset type by calling `CreateDataset`\. For more information, see [Deleting a dataset](delete-dataset.md)\.

After you create the datasets, you can create the model\. For more information, see [Training a model \(SDK\)](model-train.md#create-model-sdk)\. 

You can view the labeled images \(JSON lines\) within a dataset by calling `ListDatasetEntries`\. You can add labeled images by calling `UpdateDatasetEntries`\. 

To view information about the test and training datasets, see [Viewing your datasets](view-datasets.md)\. 

**To create a dataset \(SDK\)**

1. If you haven't already done so, do the following:

   1. Create or update an IAM user with `AmazonLookoutVisionFullAccess` and permissions\. For more information, see [Step 2: Create an IAM administrator user and group](su-account-user.md)\.

   1. Install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 4: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

1. Use the following example code to create a model\.

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
     --dataset-source '{ "GroundTruthManifest": { "S3Object": { "Bucket": "bucket", "Key": "manifest file" } } }'
   ```

------
#### [ Python ]

   Change the following values:
   + `project_name` to the name of the project that you want to associate the dataset with\.
   + `dataset_type` to the type of dataset that you want to create \(`train` or `test`\)\.
   + `bucket` to the name of the Amazon S3 bucket that contains the manifest file\. 
   + `manifest_file` to the path and file name of the manifest file within `bucket`\. 

   ```
   #Copyright 2021 Amazon.com, Inc. or its affiliates. All Rights Reserved.
   
   import boto3
   import json
   
   def create_dataset(project_name, bucket, manifest_file, dataset_type):
   
       client=boto3.client('lookoutvision')
   
   
       try: 
           #Create a project
           print('Creating dataset...')
           dataset=json.loads('{ "GroundTruthManifest": { "S3Object": { "Bucket": "' + bucket + '", "Key": "'+ manifest_file + '" } } }')
   
           response=client.create_dataset(ProjectName=project_name, DatasetType=dataset_type, DatasetSource=dataset)
           print('Dataset Status: ' + response['DatasetMetadata']['Status'])
           print('Dataset Status Message: ' + response['DatasetMetadata']['StatusMessage'])
           print('Dataset Type: ' + response['DatasetMetadata']['DatasetType'])
           print('Done...')
   
       
       except Exception as e:
           print(e)
       
   def main():
       project_name='my-project'
       bucket = 'bucket'
       manifest_file = 'input.manifest'
       dataset_type ='train'
       create_dataset(project_name, bucket, manifest_file, dataset_type)
   
   
   
   if __name__ == "__main__":
       main()
   ```

------

1. Repeat the previous step and provide values for the other dataset type\.

## Adding an image to a dataset<a name="add-labeled-images-sdk"></a>

To add labeled images to a dataset, use the [UpdateDatasetEntries](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_UpdateDatasetEntries) operation\.