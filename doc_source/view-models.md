# Viewing your models<a name="view-models"></a>

A project can have multiple versions of a model\. You can use the console to view the models in a project\. You can also use the `ListModels` operation\.

**Note**  
The list of models is eventually consistent\. If you create a model, you might have to wait a short while before the models list is up to date\. 

## Viewing your models \(console\)<a name="view-models-console"></a>

Perform the steps in the following procedure to view the your project's models in the console\. 

**To view your models \(console\)**

1. Open the Amazon Lookout for Vision console at [ https://console\.aws\.amazon\.com/lookoutvision/]( https://console.aws.amazon.com/lookoutvision/)\.

1. Choose **Get started**\. 

1. In the left navigation pane, choose **Projects**\. 

1. On the **Projects** page, select the project that contains the models that you want to view\.

1. In the left navigation pane, choose **Models** and then view the model details\.

## Viewing your models \(SDK\)<a name="view-models-sdk"></a>

To get view the versions of a model you use the `ListModels` operation\. To get information about a specific model version, use the `DescribeModel` operation\. The following example lists all the model versions in a project and then displays performance and output configuration information for individual model versions\.

**To view your models \(SDK\)**

1. If you haven't already done so, do the following:

   1. Create or update an IAM user with permissions to access Amazon Lookout for Vision\. For more information, see [Step 3: Set up permissions](su-setup-permissions.md)\. 

   1. Install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 5: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

1. Use the following example code to list your models and get information about a model\.

------
#### [ CLI ]

   Use the `list-models` command to list the models in a project\.

   Change the following value:
   + `project-name` to the name of the project that contains the model that you want to view\.

   ```
   aws lookoutvision list-models --project-name project name
   ```

   Use the `describe-model` command to get information about a model\. Change the following values:
   + `project-name` to the name of the project that contains the model that you want to view\.
   + `model-version` to the version of the model that you want to describe\.

   ```
   aws lookoutvision describe-model --project-name project name\
       --model-version model version
   ```

------
#### [ Python ]

   This code is taken from the AWS Documentation SDK examples GitHub repository\. See the full example [here](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/python/example_code/lookoutvision/train_host.py)\. 

   ```
       @staticmethod
       def describe_models(lookoutvision_client, project_name):
           """
           Gets information about all models in a Lookout for Vision project.
   
           :param lookoutvision_client: A Boto3 Lookout for Vision client.
           :param project_name: The name of the project that you want to use.
           """
           try:
               response = lookoutvision_client.list_models(ProjectName=project_name)
               print("Project: " + project_name)
               for model in response["Models"]:
                   Models.describe_model(
                       lookoutvision_client, project_name, model["ModelVersion"])
                   print()
               print("Done...")
           except ClientError:
               logger.exception("Couldn't list models.")
               raise
   ```

------
#### [ Java V2 ]

   This code is taken from the AWS Documentation SDK examples GitHub repository\. See the full example [here](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/javav2/example_code/lookoutvision/src/main/java/com/example/lookoutvision/ListModels.java)\. 

   ```
   /**
    * Lists the models in an Amazon Lookout for Vision project.
    * 
    * @param lfvClient   An Amazon Lookout for Vision client.
    * @param projectName The name of the project that contains the models that
    *                    you want to list.
    * @return List <Metadata> A list of models in the project.
    */
   public static List<ModelMetadata> listModels(LookoutVisionClient lfvClient, String projectName)
                   throws LookoutVisionException {
   
           ListModelsRequest listModelsRequest = ListModelsRequest.builder()
                           .projectName(projectName)
                           .build();
   
           // Get a list of models in the supplied project.
           ListModelsResponse response = lfvClient.listModels(listModelsRequest);
   
           for (ModelMetadata model : response.models()) {
                   logger.log(Level.INFO, "Model ARN: {0}\nVersion: {1}\nStatus: {2}\nMessage: {3}", new Object[] {
                                   model.modelArn(),
                                   model.modelVersion(),
                                   model.statusMessage(),
                                   model.statusAsString() });
           }
   
           return response.models();
   
   }
   ```

------