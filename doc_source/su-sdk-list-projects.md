# Call an Amazon Lookout for Vision operation<a name="su-sdk-list-projects"></a>

Run the following code to confirm that you can make calls to the Amazon Lookout for Vision API\. The code lists the projects in your AWS account, in the current AWS Region\. If you haven't previously created a project, the response is empty, but does confirm that you can call the `ListProjects` operation\. 

If the call fails, check that the IAM user that you use has the correct permissions\. Also check the AWS Region that you using as Amazon Lookout for Vision is not available in all AWS Regions\.

In general, calling an example function requires an AWS SDK Lookout for Vision client and any other required parameters\. The AWS SDK Lookout for Vision client is declared in the main function\. 

**To call an Amazon Lookout for Vision operation**

1. If you haven't already done so, do the following:

   1. Create or update an IAM user with permissions to access Amazon Lookout for Vision\. For more information, see [Step 3: Set up permissions](su-setup-permissions.md)\. 

   1. Install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 5: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

1. Use the following example code to view your projects\.

------
#### [ CLI ]

   Use the `list-projects` command to list the projects in your account\.

   ```
   aws lookoutvision list-projects
   ```

------
#### [ Python ]

   This code is taken from the AWS Documentation SDK examples GitHub repository\. See the full example [here](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/python/example_code/lookoutvision/train_host.py)\. 

   ```
   # Copyright Amazon.com, Inc. or its affiliates. All Rights Reserved.
   # SPDX-License-Identifier: Apache-2.0
   
   
   from botocore.exceptions import ClientError
   import boto3
   
   class GettingStarted:
   
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
                   print("ARN: " + project["ProjectArn"])
                   print()
               print("Done!")
           except ClientError:
               raise
   def main():
       
       lookoutvision_client = boto3.client("lookoutvision")
       GettingStarted.list_projects(lookoutvision_client)
   
   if __name__ == "__main__":
       main()
   ```

------
#### [ Java V2 ]

   This code is taken from the AWS Documentation SDK examples GitHub repository\. See the full example [here](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/javav2/example_code/lookoutvision/src/main/java/com/example/lookoutvision/ListProjects.java)\. 

   ```
   /*
      Copyright Amazon.com, Inc. or its affiliates. All Rights Reserved.
      SPDX-License-Identifier: Apache-2.0
   */
   
   // snippet-start:[lookoutvision.java2.gettingstarted.complete]
   
   package com.example.lookoutvision;
   
   import software.amazon.awssdk.services.lookoutvision.LookoutVisionClient;
   import software.amazon.awssdk.services.lookoutvision.model.ProjectMetadata;
   import software.amazon.awssdk.services.lookoutvision.paginators.ListProjectsIterable;
   import software.amazon.awssdk.services.lookoutvision.model.ListProjectsRequest;
   import software.amazon.awssdk.services.lookoutvision.model.LookoutVisionException;
   
   import java.util.ArrayList;
   import java.util.List;
   import java.util.logging.Level;
   import java.util.logging.Logger;
   
   public class GettingStarted {
   
       public static final Logger logger = Logger.getLogger(GettingStarted.class.getName());
   
       /**
        * Lists the Amazon Lookoutfor Vision projects in the current AWS account and
        * AWS
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
   
       public static void main(String[] args) throws Exception {
   
           try {
   
               // Get the Lookout for Vision client.
               LookoutVisionClient lfvClient = LookoutVisionClient.builder().build();
   
               List<ProjectMetadata> projects = Projects.listProjects(lfvClient);
   
               System.out.printf("Projects%n--------%n");
   
               for (ProjectMetadata project : projects) {
                   System.out.printf("Name: %s%n", project.projectName());
                   System.out.printf("ARN: %s%n", project.projectArn());
                   System.out.printf("Date: %s%n%n", project.creationTimestamp().toString());
               }
   
           } catch (LookoutVisionException lfvError) {
               logger.log(Level.SEVERE, "Could not list projects: {0}: {1}",
                       new Object[] { lfvError.awsErrorDetails().errorCode(),
                               lfvError.awsErrorDetails().errorMessage() });
               System.out.println(String.format("Could not list projects: %s", lfvError.getMessage()));
               System.exit(1);
           }
   
       }
   
   }
   // snippet-end:[lookoutvision.java2.gettingstarted.complete]
   ```

------