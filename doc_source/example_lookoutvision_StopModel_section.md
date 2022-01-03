# Stop a Lookout for Vision model using an AWS SDK<a name="example_lookoutvision_StopModel_section"></a>

The following code example shows how to stop a Lookout for Vision model\.

For more information, see [Stopping your model](https://docs.aws.amazon.com/lookout-for-vision/latest/developer-guide/run-stop-model.html)\.

------
#### [ Python ]

**SDK for Python \(Boto3\)**  
  

```
class Hosting:

    @staticmethod
    def stop_model(lookoutvision_client, project_name, model_version):
        """
        Stops a running Lookout for Vision Model.

        :param lookoutvision_client: A Boto3 Lookout for Vision client.
        :param project_name: The name of the project that contains the version of
                             the model that you want to stop hosting.
        :param model_version:  The version of the model that you want to stop hosting.
        """
        try:
            logger.info("Stopping model version %s for %s", model_version, project_name)
            response = lookoutvision_client.stop_model(
                ProjectName=project_name, ModelVersion=model_version)
            logger.info("Stopping hosting...")

            status = response["Status"]
            finished = False

            # Wait until stopped or failed.
            while finished is False:
                model_description = lookoutvision_client.describe_model(
                    ProjectName=project_name, ModelVersion=model_version)
                status = model_description["ModelDescription"]["Status"]

                if status == "STOPPING_HOSTING":
                    logger.info("Host stopping in progress...")
                    time.sleep(10)
                    continue

                if status == "TRAINED":
                    logger.info("Model is no longer hosted.")
                    finished = True
                    continue

                logger.info("Failed to stop model: %s ", status)
                finished = True

            if status != "TRAINED":
                logger.error("Error stopping model: %s", status)
                raise Exception(f"Error stopping model: {status}")
        except ClientError:
            logger.exception("Couldn't stop hosting model.")
            raise
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/python/example_code/lookoutvision#code-examples)\. 
+  For API details, see [StopModel](https://docs.aws.amazon.com/goto/boto3/lookoutvision-2020-11-20/StopModel) in *AWS SDK for Python \(Boto3\) API Reference*\. 

------

For a complete list of AWS SDK developer guides and code examples, including help getting started and information about previous versions, see [Using Lookout for Vision with an AWS SDK](getting-started-sdk.md#sdk-general-information-section)\.