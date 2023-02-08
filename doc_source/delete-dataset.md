# Deleting a dataset<a name="delete-dataset"></a>

You can delete a dataset from a project by using the console or the `DeleteDataset` operation\. The images referenced by a dataset aren't deleted\. If you delete the test dataset from a project that has a training and a test dataset, the project reverts to a single dataset project—the remaining dataset is split during training to create a training and test dataset\. If you delete the training dataset, you can't train a model in the project until you create a new training dataset\.

## Deleting a dataset \(console\)<a name="view-datasets-console"></a>

Perform the steps in the following procedure to delete a dataset\. If you delete all of the datasets in a project, the **Create dataset** page is displayed\.

**To delete a dataset \(console\)**

1. Open the Amazon Lookout for Vision console at [ https://console\.aws\.amazon\.com/lookoutvision/]( https://console.aws.amazon.com/lookoutvision/)\.

1. Choose **Get started**\. 

1. In the left navigation pane, choose **Projects**\. 

1. On the **Projects** page, select the project that contains the dataset that you want to delete\.

1. In the left navigation pane, choose **Dataset**\.

1. Choose **Actions** and then select the dataset that you want to delete\. 

1. In the **Delete** dialog box, enter **delete** to confirm that you want to delete the dataset\.

1. Choose **Delete training dataset** or **Delete test dataset** to delete the dataset\. 

## Deleting a dataset \(SDK\)<a name="delete-dataset-sdk"></a>

Use the `DeleteDataset` operation to delete a dataset\.

**To delete a dataset \(SDK\)**

1. If you haven't already done so, install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 4: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

1. Use the following example code to delete a model\.

------
#### [ CLI ]

   Change the value of the following
   + `project-name` to the name of the project that contains the model that you want to delete\.
   + `dataset-type` to either `train` or `test`, depending on which dataset you want to delete\. If you have a single dataset project, specify `train` to delete the dataset\.

   ```
   aws lookoutvision delete-dataset --project-name project name\
     --dataset-type dataset type \
     --profile lookoutvision-access
   ```

------
#### [ Python ]

   This code is taken from the AWS Documentation SDK examples GitHub repository\. See the full example [here](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/python/example_code/lookoutvision/train_host.py)\. 

   ```
       @staticmethod
       def delete_dataset(lookoutvision_client, project_name, dataset_type):
           """
           Deletes a Lookout for Vision dataset
   
           :param lookoutvision_client: A Boto3 Lookout for Vision client.
           :param project_name: The name of the project that contains the dataset that
                                you want to delete.
           :param dataset_type: The type (train or test) of the dataset that you
                                want to delete.
           """
           try:
               logger.info(
                   "Deleting the %s dataset for project %s.", dataset_type, project_name)
               lookoutvision_client.delete_dataset(
                   ProjectName=project_name, DatasetType=dataset_type)
               logger.info("Dataset deleted.")
           except ClientError:
               logger.exception("Service error: Couldn't delete dataset.")
               raise
   ```

------
#### [ Java V2 ]

   This code is taken from the AWS Documentation SDK examples GitHub repository\. See the full example [here](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/javav2/example_code/lookoutvision/src/main/java/com/example/lookoutvision/DeleteDataset.java)\. 

   ```
   /**
    * Deletes the train or test dataset in an Amazon Lookout for Vision project.
    * 
    * @param lfvClient   An Amazon Lookout for Vision client.
    * @param projectName The name of the project in which you want to delete a
    *                    dataset.
    * @param datasetType The type of the dataset that you want to delete (train or
    *                    test).
    * @return Nothing.
    */
   public static void deleteDataset(LookoutVisionClient lfvClient, String projectName, String datasetType)
                   throws LookoutVisionException {
   
           logger.log(Level.INFO, "Deleting {0} dataset for project {1}",
                           new Object[] { datasetType, projectName });
   
           DeleteDatasetRequest deleteDatasetRequest = DeleteDatasetRequest.builder()
                           .projectName(projectName)
                           .datasetType(datasetType)
                           .build();
   
           lfvClient.deleteDataset(deleteDatasetRequest);
   
           logger.log(Level.INFO, "Deleted {0} dataset for project {1}",
                           new Object[] { datasetType, projectName });
   
   }
   ```

------