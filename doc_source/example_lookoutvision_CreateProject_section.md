# Create a Lookout for Vision project using an AWS SDK<a name="example_lookoutvision_CreateProject_section"></a>

The following code example shows how to create a Lookout for Vision project\.

For more information, see [Creating your project](https://docs.aws.amazon.com/lookout-for-vision/latest/developer-guide/model-create-project.html)\.

------
#### [ Python ]

**SDK for Python \(Boto3\)**  
  

```
class Projects:

    @staticmethod
    def create_project(lookoutvision_client, project_name):
        """
        Creates a new Lookout for Vision project.

        :param lookoutvision_client: A Boto3 Lookout for Vision client.
        :param  project_name: The name for the new project.
        :return project_arn: The ARN of the new project.
        """
        try:
            logger.info("Creating project: %s", project_name)
            response = lookoutvision_client.create_project(ProjectName=project_name)
            project_arn = response["ProjectMetadata"]["ProjectArn"]
            logger.info("project ARN: %s", project_arn)
        except ClientError:
            logger.exception("Couldn't create project %s.", project_name)
            raise
        else:
            return project_arn
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/python/example_code/lookoutvision#code-examples)\. 
+  For API details, see [CreateProject](https://docs.aws.amazon.com/goto/boto3/lookoutvision-2020-11-20/CreateProject) in *AWS SDK for Python \(Boto3\) API Reference*\. 

------

For a complete list of AWS SDK developer guides and code examples, including help getting started and information about previous versions, see [Using Lookout for Vision with an AWS SDK](getting-started-sdk.md#sdk-general-information-section)\.