# Create a Lookout for Vision model using an AWS SDK<a name="example_lookoutvision_CreateModel_section"></a>

The following code example shows how to create a Lookout for Vision model\.

For more information, see [Training your model](https://docs.aws.amazon.com/lookout-for-vision/latest/developer-guide/model-train.html)\.

------
#### [ Python ]

**SDK for Python \(Boto3\)**  
  

```
class Models:

    @staticmethod
    def create_model(
            lookoutvision_client, project_name, training_results, tag_key=None,
            tag_key_value=None):
        """
        Creates a version of a Lookout for Vision model.

        :param lookoutvision_client: A Boto3 Lookout for Vision client.
        :param project_name: The name of the project in which you want to create a
                             model.
        :param training_results: The Amazon S3 location where training results are stored.
        :param tag_key: The key for a tag to add to the model.
        :param tag_key_value - A value associated with the tag_key.
        return: The model status and version.
        """
        try:
            logger.info("Training model...")
            output_bucket, output_folder = training_results.replace(
                "s3://", "").split("/", 1)
            output_config = {
                "S3Location": {"Bucket": output_bucket, "Prefix": output_folder}}
            tags = []
            if tag_key is not None:
                tags = [{"Key": tag_key, "Value": tag_key_value}]

            response = lookoutvision_client.create_model(
                ProjectName=project_name, OutputConfig=output_config, Tags=tags)

            logger.info("ARN: %s", response["ModelMetadata"]["ModelArn"])
            logger.info("Version: %s", response["ModelMetadata"]["ModelVersion"])
            logger.info("Started training...")

            print("Training started. Training might take several hours to complete.")

            # Wait until training completes.
            finished = False
            status = "UNKNOWN"
            while finished is False:
                model_description = lookoutvision_client.describe_model(
                    ProjectName=project_name,
                    ModelVersion=response["ModelMetadata"]["ModelVersion"])
                status = model_description["ModelDescription"]["Status"]

                if status == "TRAINING":
                    logger.info("Model training in progress...")
                    time.sleep(600)
                    continue

                if status == "TRAINED":
                    logger.info("Model was successfully trained.")
                else:
                    logger.info(
                        "Model training failed: %s ",
                        model_description["ModelDescription"]["StatusMessage"])
                finished = True
        except ClientError:
            logger.exception("Couldn't train model.")
            raise
        else:
            return status, response["ModelMetadata"]["ModelVersion"]
```
+  Find instructions and more code on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/python/example_code/lookoutvision#code-examples)\. 
+  For API details, see [CreateModel](https://docs.aws.amazon.com/goto/boto3/lookoutvision-2020-11-20/CreateModel) in *AWS SDK for Python \(Boto3\) API Reference*\. 

------

For a complete list of AWS SDK developer guides and code examples, including help getting started and information about previous versions, see [Using Lookout for Vision with an AWS SDK](getting-started-sdk.md#sdk-general-information-section)\.