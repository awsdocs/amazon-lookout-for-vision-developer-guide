# Delete a Lookout for Vision dataset using an AWS SDK<a name="example_lookoutvision_DeleteDataset_section"></a>

The following code example shows how to delete a Lookout for Vision dataset\.

For more information, see [Deleting a dataset](https://docs.aws.amazon.com/lookout-for-vision/latest/developer-guide/delete-dataset.html)\.

------
#### [ Python ]

**SDK for Python \(Boto3\)**  
  

```
class Datasets:

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
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/python/example_code/lookoutvision#code-examples)\. 
+  For API details, see [DeleteDataset](https://docs.aws.amazon.com/goto/boto3/lookoutvision-2020-11-20/DeleteDataset) in *AWS SDK for Python \(Boto3\) API Reference*\. 

------

For a complete list of AWS SDK developer guides and code examples, including help getting started and information about previous versions, see [Using Lookout for Vision with an AWS SDK](getting-started-sdk.md#sdk-general-information-section)\.