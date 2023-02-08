# Viewing your projects<a name="view-projects"></a>

You can get a list of Amazon Lookout for Vision projects and information about individual projects from the console or by using the AWS SDK\.

**Note**  
The list of projects is eventually consistent\. If you create or delete a project, you might have to wait a short while before the projects list is up to date\. 

## Viewing your projects \(console\)<a name="view-projects-console"></a>

Perform the steps in the following procedure to view your projects in the console\. 

**To view your projects**

1. Open the Amazon Lookout for Vision console at [ https://console\.aws\.amazon\.com/lookoutvision/]( https://console.aws.amazon.com/lookoutvision/)\.

1. Choose **Get started**\. 

1. In the left navigation pane, choose **Projects**\. The projects view is shown\.

1. Choose a project name to see the project's details\.

## Viewing your projects \(SDK\)<a name="view-projects-sdk"></a>

A project manages the datasets and models for a single use case\. For example, detecting anomalies in machine parts\. The following example calls `ListProjects` to get a list of your projects\. 

**To view your projects \(SDK\)**

1. If you haven't already done so, install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 4: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

1. Use the following example code to view your projects\.

------
#### [ CLI ]

   Use the `list-projects` command to list the projects in your account\.

   ```
   aws lookoutvision list-projects \
     --profile lookoutvision-access
   ```

   Use the `describe-project` command to get information about a project\.

   Change the value of `project-name` to the name of the project that you want to describe\.

   ```
   aws lookoutvision describe-project --project-name project_name \
     --profile lookoutvision-access
   ```

------
#### [ Python ]

   This code is taken from the AWS Documentation SDK examples GitHub repository\. See the full example [here](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/python/example_code/lookoutvision/train_host.py)\. 

   ```
       @staticmethod
       def list_projects(lookoutvision_client):
           """
           Lists information about the projects that are in in your AWS account
           and in the current AWS Region.
   
           :param lookoutvision_client: A Boto3 Lookout for Vision client.
           """
           try:
               response = lookoutvision_client.list_projects()
               for project in response["Projects"]:
                   print("Project: " + project["ProjectName"])
                   print("\tARN: " + project["ProjectArn"])
                   print("\tCreated: " + str(["CreationTimestamp"]))
                   print("Datasets")
                   project_description = lookoutvision_client.describe_project(
                       ProjectName=project["ProjectName"])
                   if not project_description["ProjectDescription"]["Datasets"]:
                       print("\tNo datasets")
                   else:
                       for dataset in project_description["ProjectDescription"]["Datasets"]:
                           print(f"\ttype: {dataset['DatasetType']}")
                           print(f"\tStatus: {dataset['StatusMessage']}")
   
                   print("Models")
                   response_models = lookoutvision_client.list_models(
                       ProjectName=project["ProjectName"])
                   if not response_models["Models"]:
                       print("\tNo models")
                   else:
                       for model in response_models["Models"]:
                           Models.describe_model(
                               lookoutvision_client, project["ProjectName"],
                               model["ModelVersion"])
   
                   print("------------------------------------------------------------\n")
               print("Done!")
           except ClientError:
               logger.exception("Problem listing projects.")
               raise
   ```

------
#### [ Java V2 ]

   This code is taken from the AWS Documentation SDK examples GitHub repository\. See the full example [here](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/javav2/example_code/lookoutvision/src/main/java/com/example/lookoutvision/ListProjects.java)\. 

   ```
   /**
    * Lists the Amazon Lookout for Vision projects in the current AWS account and AWS
    * Region.
    * 
    * @param lfvClient   An Amazon Lookout for Vision client.
    * @param projectName The name of the project that you want to create.
    * @return List<ProjectMetadata> Metadata for each project.
    */
   public static List<ProjectMetadata> listProjects(LookoutVisionClient lfvClient)
                   throws LookoutVisionException {
   
           logger.log(Level.INFO, "Getting projects:");
           ListProjectsRequest listProjectsRequest = ListProjectsRequest.builder()
                           .maxResults(100)
                           .build();
   
           List<ProjectMetadata> projectMetadata = new ArrayList<>();
   
           ListProjectsIterable projects = lfvClient.listProjectsPaginator(listProjectsRequest);
   
           projects.stream().flatMap(r -> r.projects().stream())
                           .forEach(project -> {
                                   projectMetadata.add(project);
                                   logger.log(Level.INFO, project.projectName());
                           });
   
           logger.log(Level.INFO, "Finished getting projects.");
   
           return projectMetadata;
   
   }
   ```

------