# Delete a Lookout for Vision model using an AWS SDK<a name="example_lookoutvision_DeleteModel_section"></a>

The following code example shows how to delete a Lookout for Vision model\.

For more information, see [Deleting a model](https://docs.aws.amazon.com/lookout-for-vision/latest/developer-guide/delete-model.html)\.

------
#### [ Python ]

**SDK for Python \(Boto3\)**  
  

```
class Models:

    @staticmethod
    def delete_model(lookoutvision_client, project_name, model_version):
        """
        Deletes a Lookout for Vision model. The model must first be stopped and can't
        be in training.

        :param lookoutvision_client: A Boto3 Lookout for Vision client.
        :param project_name: The name of the project that contains the desired model.
        :param model_version: The version of the model that you want to delete.
        """
        try:
            logger.info("Deleting model: %s", model_version)
            lookoutvision_client.delete_model(
                ProjectName=project_name, ModelVersion=model_version)

            model_exists = True
            while model_exists:
                response = lookoutvision_client.list_models(ProjectName=project_name)

                model_exists = False
                for model in response["Models"]:
                    if model["ModelVersion"] == model_version:
                        model_exists = True

                if model_exists is False:
                    logger.info("Model deleted")
                else:
                    logger.info("Model is being deleted...")
                    time.sleep(2)

            logger.info("Deleted Model: %s", model_version)
        except ClientError:
            logger.exception("Couldn't delete model.")
            raise
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/python/example_code/lookoutvision#code-examples)\. 
+  For API details, see [DeleteModel](https://docs.aws.amazon.com/goto/boto3/lookoutvision-2020-11-20/DeleteModel) in *AWS SDK for Python \(Boto3\) API Reference*\. 

------

For a complete list of AWS SDK developer guides and code examples, including help getting started and information about previous versions, see [Using Lookout for Vision with an AWS SDK](getting-started-sdk.md#sdk-general-information-section)\.