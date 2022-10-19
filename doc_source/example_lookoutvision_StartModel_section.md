# Start a Lookout for Vision model using an AWS SDK<a name="example_lookoutvision_StartModel_section"></a>

The following code example shows how to start a Lookout for Vision model\.

**Note**  
The source code for these examples is in the [AWS Code Examples GitHub repository](https://github.com/awsdocs/aws-doc-sdk-examples)\. Have feedback on a code example? [Create an Issue](https://github.com/awsdocs/aws-doc-sdk-examples/issues/new/choose) in the code examples repo\. 

For more information, see [Starting your model](https://docs.aws.amazon.com/lookout-for-vision/latest/developer-guide/run-start-model.html)\.

------
#### [ Python ]

**SDK for Python \(Boto3\)**  
 To learn how to set up and run this example, see [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/python/example_code/lookoutvision#code-examples)\. 
  

```
class Hosting:

    @staticmethod
    def start_model(
            lookoutvision_client, project_name, model_version, min_inference_units):
        """
        Starts the hosting of a Lookout for Vision model.

        :param lookoutvision_client: A Boto3 Lookout for Vision client.
        :param project_name:  The name of the project that contains the version of the
                              model that you want to start hosting.
        :param model_version: The version of the model that you want to start hosting.
        :param min_inference_units: The number of inference units to use for hosting.
        """
        try:
            logger.info(
                "Starting model version %s for project %s", model_version, project_name)
            lookoutvision_client.start_model(
                ProjectName=project_name,
                ModelVersion=model_version,
                MinInferenceUnits=min_inference_units)
            print("Starting hosting...")

            status = ""
            finished = False

            # Wait until hosted or failed.
            while finished is False:
                model_description = lookoutvision_client.describe_model(
                    ProjectName=project_name, ModelVersion=model_version)
                status = model_description["ModelDescription"]["Status"]

                if status == "STARTING_HOSTING":
                    logger.info("Host starting in progress...")
                    time.sleep(10)
                    continue

                if status == "HOSTED":
                    logger.info("Model is hosted and ready for use.")
                    finished = True
                    continue

                logger.info("Model hosting failed and the model can't be used.")
                finished = True

            if status != "HOSTED":
                logger.error("Error hosting model: %s", status)
                raise Exception(f"Error hosting model: {status}")
        except ClientError:
            logger.exception("Couldn't host model.")
            raise
```
+  For API details, see [StartModel](https://docs.aws.amazon.com/goto/boto3/lookoutvision-2020-11-20/StartModel) in *AWS SDK for Python \(Boto3\) API Reference*\. 

------

For a complete list of AWS SDK developer guides and code examples, see [Using this service with an AWS SDK](sdk-general-information-section.md)\. This topic also includes information about getting started and details about previous SDK versions\.