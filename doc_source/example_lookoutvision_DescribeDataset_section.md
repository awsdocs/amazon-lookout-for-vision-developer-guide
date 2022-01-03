# Describe a Lookout for Vision dataset using an AWS SDK<a name="example_lookoutvision_DescribeDataset_section"></a>

The following code example shows how to describe a Lookout for Vision dataset\.

For more information, see [Viewing your dataset](https://docs.aws.amazon.com/lookout-for-vision/latest/developer-guide/view-datasets.html)\.

------
#### [ Python ]

**SDK for Python \(Boto3\)**  
  

```
class Datasets:

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
            print(f"Message: {response['DatasetDescription']['StatusMessage']}")
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
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/python/example_code/lookoutvision#code-examples)\. 
+  For API details, see [DescribeDataset](https://docs.aws.amazon.com/goto/boto3/lookoutvision-2020-11-20/DescribeDataset) in *AWS SDK for Python \(Boto3\) API Reference*\. 

------

For a complete list of AWS SDK developer guides and code examples, including help getting started and information about previous versions, see [Using Lookout for Vision with an AWS SDK](getting-started-sdk.md#sdk-general-information-section)\.