# Creating your project<a name="model-create-project"></a>

An Amazon Lookout for Vision project is a grouping of the resources needed to create and manage a Lookout for Vision model\. A project manages the following:
+ **Dataset** – The images and image labels used to train a model\. For more information, see [Creating your dataset](model-create-dataset.md)\.
+ **Model** – The software that you train to detect anomalies\. You can have multiple versions of a model\. For more information, see [Training your model](model-train.md)\. 

We recommend that you use a project for a single use case, such as detecting anomalies in a single type of machine part\. 

**Note**  
You can use AWS CloudFormation to provision and configure Amazon Lookout for Vision projects\. For more information, see [Creating Amazon Lookout for Vision projects with AWS CloudFormation](creating-projects-with-cloudformation.md)\.

To view your projects, see [Viewing your projects](view-projects.md) or open the [Using the Amazon Lookout for Vision dashboard](dashboard.md)\. To delete a model, see [Deleting a model](delete-model.md)\. 

**Topics**
+ [Creating a project \(console\)](#create-project-console)
+ [Creating a project \(SDK\)](#create-project-sdk)

## Creating a project \(console\)<a name="create-project-console"></a>

The following procedure shows you how to create a project using the console\.

**To create a project \(console\)**

1. Open the Amazon Lookout for Vision console at [ https://console\.aws\.amazon\.com/lookoutvision/]( https://console.aws.amazon.com/lookoutvision/)\.

1. In the left navigation pane, choose **Projects**\.

1. Choose **Create project**\. 

1. In **Project name**, enter a name for your project\.

1. Choose **Create project**\. The details page for your project is displayed\.

1. Follow the steps in [Creating your dataset](model-create-dataset.md) to create your dataset\.

## Creating a project \(SDK\)<a name="create-project-sdk"></a>

You use the [CreateProject](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_CreateProject) operation to create an Amazon Lookout for Vision project\. The response from `CreateProject` includes the project name and the Amazon Resource Name \(ARN\) of the project\. Afterwards, call [CreateDataset](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_CreateDataset) to add a training and a test dataset to your project\. For more information, see [Creating a dataset with a manifest file \(SDK\)](create-dataset-sdk.md)\. 

To view the projects that you have created in a project, call `ListProjects`\. For more information, see [Viewing your projects](view-projects.md)\. 

**To create a project \(SDK\)**

1. If you haven't already done so, do the following:

   1. Create or update an IAM user with permissions to access Amazon Lookout for Vision\. For more information, see [Step 3: Set up permissions](su-setup-permissions.md)\. 

   1. Install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 5: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

1. Use the following example code to create a model\.

------
#### [ CLI ]

   Change the value of `project-name` to the name that you want to use for the project\.

   ```
   aws lookoutvision create-project --project-name project name
   ```

------
#### [ Python ]

   This code is taken from the AWS Documentation SDK examples GitHub repository\. See the full example [here](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/python/example_code/lookoutvision/train_host.py)\. 

   ```
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

------
#### [ Java V2 ]

   This code is taken from the AWS Documentation SDK examples GitHub repository\. See the full example [here](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/javav2/example_code/lookoutvision/src/main/java/com/example/lookoutvision/CreateProject.java)\. 

   ```
   /**
    * Creates an Amazon Lookout for Vision project.
    * 
    * @param lfvClient   An Amazon Lookout for Vision client.
    * @param projectName The name of the project that you want to create.
    * @return ProjectMetadata Metadata information about the created project.
    */
   public static ProjectMetadata createProject(LookoutVisionClient lfvClient, String projectName)
                   throws LookoutVisionException {
   
           logger.log(Level.INFO, "Creating project: {0}", projectName);
           CreateProjectRequest createProjectRequest = CreateProjectRequest.builder().projectName(projectName)
                           .build();
   
           CreateProjectResponse response = lfvClient.createProject(createProjectRequest);
   
           logger.log(Level.INFO, "Project created. ARN: {0}", response.projectMetadata().projectArn());
   
           return response.projectMetadata();
   
   }
   ```

------

1. Follow the steps in [Creating a dataset using an Amazon SageMaker Ground Truth manifest file](create-dataset-ground-truth.md) to create your dataset\.