# Delete a Lookout for Vision project using an AWS SDK<a name="example_lookoutvision_DeleteProject_section"></a>

The following code example shows how to delete a Lookout for Vision project\.

For more information, see [Deleting a project](https://docs.aws.amazon.com/lookout-for-vision/latest/developer-guide/delete-project.html)\.

------
#### [ Python ]

**SDK for Python \(Boto3\)**  
  

```
class Projects:

    @staticmethod
    def delete_project(lookoutvision_client, project_name):
        """
        Deletes a Lookout for Vision Model

        :param lookoutvision_client: A Boto3 Lookout for Vision client.
        :param project_name: The name of the project that you want to delete.
        """
        try:
            logger.info("Deleting project: %s", project_name)
            response = lookoutvision_client.delete_project(ProjectName=project_name)
            logger.info("Deleted project ARN: %s ", response["ProjectArn"])
        except ClientError as err:
            logger.exception("Couldn't delete project %s.", project_name)
            raise
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/python/example_code/lookoutvision#code-examples)\. 
+  For API details, see [DeleteProject](https://docs.aws.amazon.com/goto/boto3/lookoutvision-2020-11-20/DeleteProject) in *AWS SDK for Python \(Boto3\) API Reference*\. 

------

For a complete list of AWS SDK developer guides and code examples, including help getting started and information about previous versions, see [Using Lookout for Vision with an AWS SDK](getting-started-sdk.md#sdk-general-information-section)\.