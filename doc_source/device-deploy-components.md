# Deploying your components to a device<a name="device-deploy-components"></a>


|  | 
| --- |
| The model packaging feature is in preview release for Amazon Lookout for Vision and is subject to change\. | 

To deploy the model component and client application component to an AWS IoT Greengrass Version 2 core device you use the AWS IoT Greengrass V2 console or use the [CreateDeployment](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_CreateDeployment.html) API\. For more information, see [Create deployments](https://docs.aws.amazon.com/greengrass/v2/developerguide/create-deployments.html) or in the *AWS IoT Greengrass Version 2 Developer Guide*\. For information about updating a component that is deployed to a core device, see [Revise deployments](https://docs.aws.amazon.com/greengrass/v2/developerguide/revise-deployments.html)\. 

To deploy a component with AWS IoT Greengrass V2 you need the following permissions:
+ `greengrass:ListComponents`
+ `greengrass:ListComponentVersions`
+ `greengrass:ListCoreDevices`
+ `greengrass:CreateDeployment`
+ `greengrass:GetDeployment`

`CreateDeployment` and `GetDeployment` have dependent actions\. For more information, see [Actions defined by AWS IoT Greengrass V2](https://docs.aws.amazon.com/service-authorization/latest/reference/list_awsiotgreengrassv2.html#awsiotgreengrassv2-actions-as-permissions)\. 

For information about changing IAM permissions, see [Changing permissions for an IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_change-permissions.html)\. 

## Deploying your components \(console\)<a name="device-deploy-components-model"></a>

Use the following procedure to deploy the client application component to your core device\. The client application depends on the model component \(which in turn depends on the Lookout for Vision Edge Agent\)\. Deploying the client application component also starts the deployment of the model component and the Lookout for Vision Edge Agent\.

**To deploy your components**

1. In the AWS IoT Greengrass console at [https://console\.aws\.amazon\.com/iot/](https://console.aws.amazon.com/greengrass/) navigation menu, choose **Deployments**\.

1. On the **Components** page, on the **My components** tab, choose `com.lookoutvison.EdgeAgentPythonExample`\.

1. On the `com.lookoutvison.EdgeAgentPythonExample` page, choose **Deploy**\.

1. From `Add to deployment`, choose one of the following:

   1. To merge this component to an existing deployment on your target device, choose **Add to existing deployment**, and then select the deployment that you want to revise\.

   1. To create a new deployment on your target device, choose **Create new deployment**\. If you have an existing deployment on your device, choosing this step replaces the existing deployment\.

1. On the **Specify target** page, do the following:

   1. Under **Deployment information**, enter or modify the friendly name for your deployment\.

   1. Under **Deployment targets**, select a target for your deployment, and choose **Next**\. You cannot change the deployment target if you are revising an existing deployment\.

1. On the **Configure advanced settings** page, keep the default configuration settings, and choose **Next**\.

1. On the **Review** page, choose **Deploy**\.

## Deploying the components \(SDK\)<a name="device-deploy-components-sdk"></a>

Use the following procedure to deploy the client application component, model component, and the Amazon Lookout for Vision Edge Agent to your core device\.

1. Create a ` deployment.json` file to define the deployment configuration for your components\. This file should look like the following example\.

   ```
   {
     "targetArn":"targetArn",
     "components": {
       "com.lookoutvison.EdgeAgentPythonExample": {
         "componentVersion": "1.0.0",
         "configurationUpdate": {
         }
       }
     }
   }
   ```
   + In the `targetArn` field, replace *`targetArn`* with the Amazon Resource Name \(ARN\) of the thing or thing group to target for the deployment, in the following format:
     + Thing: `arn:aws:iot:region:account-id:thing/thingName`
     + Thing group: `arn:aws:iot:region:account-id:thinggroup/thingGroupName`

1. Check if the deployment target has an existing deployment that you want to revise\. Do the following:

   1. Run the following command to list the deployments for the deployment target\. Replace `targetArn` with the Amazon Resource Name \(ARN\) of the target AWS IoT thing or thing group\.

      ```
      aws greengrassv2 list-deployments --target-arn targetArn
      ```

      The response contains a list with the latest deployment for the target\. If the response is empty, the target doesn't have an existing deployment, and you can skip to Step 3\. Otherwise, copy the `deploymentId` from the response to use in the next step\. 

   1. Run the following command to get the deployment's details\. These details include metadata, components, and job configuration\. Replace `deploymentId` with the ID from the previous step\.

      ```
      aws greengrassv2 get-deployment --deployment-id deploymentId
      ```

   1. Copy any of the following key\-value pairs from the previous command's response into deployment\.json\. You can change these values for the new deployment\.
      + `deploymentName` — The deployment's name\.
      + `components` — The deployment's components\. To uninstall a component, remove it from this object\.
      + `deploymentPolicies` — The deployment's policies\.
      + `iotJobConfiguration` — The deployment's job configuration\.
      + `tags` — The deployment's tags\.

1. Run the following command to deploy the components on the device:

   ```
   aws greengrassv2 create-deployment \
       --cli-input-json file://path/to/deployment.json
   ```

For more information about deploying components, see [Create deployments](https://docs.aws.amazon.com/greengrass/v2/developerguide/create-deployments.html)\. For more details about deployment status, see [Check deployment status](https://docs.aws.amazon.com/greengrass/v2/developerguide/check-deployment-status.html)\.