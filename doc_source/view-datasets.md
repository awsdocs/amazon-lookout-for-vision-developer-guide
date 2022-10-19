# Viewing your datasets<a name="view-datasets"></a>

A project can have a single dataset that's used for training and testing your model\. Alternatively, You can have separate training and test datasets\. You can use the console to view your datasets\. You can also use the `DescribeDataset` operation to get information about a dataset \(training or test\)\.

## Viewing the datasets in a project \(console\)<a name="view-datasets-console"></a>

Perform the steps in the following procedure to view your project's datasets in the console\. 

**To view your datasets \(console\)**

1. Open the Amazon Lookout for Vision console at [ https://console\.aws\.amazon\.com/lookoutvision/]( https://console.aws.amazon.com/lookoutvision/)\.

1. Choose **Get started**\. 

1. In the left navigation pane, choose **Projects**\. 

1. On the **Projects** page, select the project that contains the datasets that you want to view\.

1. In the left navigation pane, choose **Dataset** to view the dataset details\. If you have a training and a test dataset, a tab for each dataset is shown\.

## Viewing the datasets in a project \(SDK\)<a name="view-datasets-sdk"></a>

You can get use the `DescribeDataset` operation to get information about the training or test dataset associated with a project\.

**To view your datasets \(SDK\)**

1. If you haven't already done so, do the following:

   1. Create or update an IAM user with permissions to access Amazon Lookout for Vision\. For more information, see [Step 3: Set up permissions](su-setup-permissions.md)\. 

   1. Install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 5: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

1. Use the following example code to view a dataset\.

------
#### [ CLI ]

   Change the following values:
   + `project-name` to the name of the project that contains the model that you want to view\.
   + `dataset-type` to the type of dataset that you want to view \(`train` or `test`\)\.

   ```
   aws lookoutvision describe-dataset --project-name project name\
     --dataset-type train or test
   ```

------
#### [ Python ]

   This code is taken from the AWS Documentation SDK examples GitHub repository\. See the full example [here](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/python/example_code/lookoutvision/train_host.py)\. 

   ```
       @staticmethod
       def describe_dataset(lookoutvision_client, project_name, dataset_type):
           """
           Gets information about a Lookout for Vision dataset.
   
           :param lookoutvision_client: A Boto3 Lookout for Vision client.
           :param project_name: The name of the project that contains the dataset that
                                you want to describe.
           :param dataset_type: The type (train or test) of the dataset that you want
                                to describe.
           """
           try:
               response = lookoutvision_client.describe_dataset(
                   ProjectName=project_name, DatasetType=dataset_type)
               print(f"Name: {response['DatasetDescription']['ProjectName']}")
               print(f"Type: {response['DatasetDescription']['DatasetType']}")
               print(f"Status: {response['DatasetDescription']['Status']}")
               print(
                   f"Message: {response['DatasetDescription']['StatusMessage']}")
               print(
                   f"Images: {response['DatasetDescription']['ImageStats']['Total']}")
               print(
                   f"Labeled: {response['DatasetDescription']['ImageStats']['Labeled']}")
               print(
                   f"Normal: {response['DatasetDescription']['ImageStats']['Normal']}")
               print(
                   f"Anomaly: {response['DatasetDescription']['ImageStats']['Anomaly']}")
           except ClientError:
               logger.exception("Service error: problem listing datasets.")
               raise
           print("Done.")
   ```

------
#### [ Java V2 ]

   This code is taken from the AWS Documentation SDK examples GitHub repository\. See the full example [here](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/javav2/example_code/lookoutvision/src/main/java/com/example/lookoutvision/DescribeDataset.java)\. 

   ```
   /**
    * Gets the description for a Amazon Lookout for Vision dataset.
    * 
    * @param lfvClient   An Amazon Lookout for Vision client.
    * @param projectName The name of the project in which you want to describe a
    *                    dataset.
    * @param datasetType The type of the dataset that you want to describe (train
    *                    or test).
    * @return DatasetDescription A description of the dataset.
    */
   public static DatasetDescription describeDataset(LookoutVisionClient lfvClient,
                   String projectName,
                   String datasetType) throws LookoutVisionException {
   
           logger.log(Level.INFO, "Describing {0} dataset for project {1}",
                           new Object[] { datasetType, projectName });
   
           DescribeDatasetRequest describeDatasetRequest = DescribeDatasetRequest.builder()
                           .projectName(projectName)
                           .datasetType(datasetType)
                           .build();
   
           DescribeDatasetResponse describeDatasetResponse = lfvClient.describeDataset(describeDatasetRequest);
           DatasetDescription datasetDescription = describeDatasetResponse.datasetDescription();
   
           logger.log(Level.INFO, "Project: {0}\n"
                           + "Created: {1}\n"
                           + "Type: {2}\n"
                           + "Total: {3}\n"
                           + "Labeled: {4}\n"
                           + "Normal: {5}\n"
                           + "Anomalous: {6}\n",
                           new Object[] {
                                           datasetDescription.projectName(),
                                           datasetDescription.creationTimestamp(),
                                           datasetDescription.datasetType(),
                                           datasetDescription.imageStats().total().toString(),
                                           datasetDescription.imageStats().labeled().toString(),
                                           datasetDescription.imageStats().normal().toString(),
                                           datasetDescription.imageStats().anomaly().toString(),
                           });
   
           return datasetDescription;
   
   }
   ```

------