--------

Amazon Lookout for Vision is in preview release and is subject to change\.

--------

# Viewing your projects<a name="view-projects"></a>

You can get a list of Amazon Lookout for Vision projects and information about individual projects from the console or by using the AWS SDK\.

## Viewing your projects \(console\)<a name="view-projects-console"></a>

Perform the steps in the following procedure to view your projects in the console\. 

**To view your projects**

1. Open the Amazon Lookout for Vision console at [ https://console\.aws\.amazon\.com/lookoutvision/]( https://console.aws.amazon.com/lookoutvision/)\.

1. Choose **Get started**\. 

1. In the left navigation pane, choose **Projects**\. The projects view is shown\.

1. Choose a project name to see the project's details\.

## Viewing your projects \(SDK\)<a name="view-projects-sdk"></a>

A project manages the datasets and models for a single use case\. For example, detecting anomalies in machine parts\. The following example calls `ListProjects` to get a list of your projects\. The example then calls `DescribeProject` to get the current status of each project\.

**To view your projects \(SDK\)**

1. If you haven't already done so, do the following:

   1. Create or update an IAM user with `AmazonLookoutVisionFullAccess` and permissions\. For more information, see [Step 2: Create an IAM administrator user and group](su-account-user.md)\.

   1. Install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 4: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

1. Use the following example code to view your projects\.

------
#### [ CLI ]

   Use the `list-projects` command to list the projects in your account\.

   ```
   aws lookoutvision list-projects
   ```

   Use the `describe-project` command to get information about a project\.

   Change the value of `project-name` to the name of the project that you want to describe\.

   ```
   aws lookoutvision describe-project --project-name project_name
   ```

------
#### [ Python ]

   ```
   #Copyright 2021 Amazon.com, Inc. or its affiliates. All Rights Reserved.
   
   import boto3
   
   def list_projects():
   
       client=boto3.client('lookoutvision')
   
       try:
           response=client.list_projects()
   
           for project in response['Projects']:
               print("Project: " + project['ProjectName'])
               print("ARN: " + project['ProjectArn']) 
               
               project_description=client.describe_project(ProjectName=project['ProjectName'])
               for dataset in project_description['ProjectDescription']['Datasets']:
                   print('Dataset type: ' + dataset['DatasetType'])
                   print('Dataset status: ' + dataset['Status'])
                   print('Dataset status: ' + dataset['StatusMessage'])
               print()
       
       except Exception as e:
           print(e)
           
       print('Done...')
       
   def main():
       list_projects()
   
   if __name__ == "__main__":
       main()
   ```

------