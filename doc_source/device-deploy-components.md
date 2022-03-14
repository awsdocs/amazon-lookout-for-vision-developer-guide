# Deploying your components to a device<a name="device-deploy-components"></a>

To deploy the model component and client application component to an AWS IoT Greengrass Version 2 core device you use the AWS IoT Greengrass V2 console or use the [CreateDeployment](https://docs.aws.amazon.com/greengrass/v2/APIReference/API_CreateDeployment.html) API\. For more information, see [Create deployments](https://docs.aws.amazon.com/greengrass/v2/developerguide/create-deployments.html) or in the *AWS IoT Greengrass Version 2 Developer Guide*\. For information about updating a component that is deployed to a core device, see [Revise deployments](https://docs.aws.amazon.com/greengrass/v2/developerguide/revise-deployments.html)\. 

**Topics**
+ [IAM permissions for deploying components](#device-deploy-components-permissions)
+ [Deploying your components \(console\)](#device-deploy-components-model)
+ [Deploying the components \(SDK\)](#device-deploy-components-sdk)

## IAM permissions for deploying components<a name="device-deploy-components-permissions"></a>

To deploy a component with AWS IoT Greengrass V2 you need the following permissions:
+ `greengrass:ListComponents`
+ `greengrass:ListComponentVersions`
+ `greengrass:ListCoreDevices`
+ `greengrass:CreateDeployment`
+ `greengrass:GetDeployment`
+ `greengrass:ListDeployments`

`CreateDeployment` and `GetDeployment` have dependent actions\. For more information, see [Actions defined by AWS IoT Greengrass V2](https://docs.aws.amazon.com/service-authorization/latest/reference/list_awsiotgreengrassv2.html#awsiotgreengrassv2-actions-as-permissions)\. 

For information about changing IAM permissions, see [Changing permissions for an IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_change-permissions.html)\. 

## Deploying your components \(console\)<a name="device-deploy-components-model"></a>

Use the following procedure to deploy the client application component to a core device\. The client application depends on the model component \(which in turn depends on the Lookout for Vision Edge Agent\)\. Deploying the client application component also starts the deployment of the model component and the Lookout for Vision Edge Agent\.

**Note**  
You can add your components to an existing deployment\. You can also deploy components to a thing group\.

To run this procedure, you must have a configured AWS IoT Greengrass V2 core device\. For more information, see [Setting up your AWS IoT Greengrass Version 2 core device](models-devices-setup-core-device.md)\.

**To deploy your components to a device**

1. Open the AWS IoT Greengrass console at [https://console\.aws\.amazon\.com/iot/](https://console.aws.amazon.com/greengrass/)\.

1. In the left navigation pane, under **Greengrass** choose **Deployments**\.

1. Under **Deployments** choose **Create**\.

1. On the **Specify target** page, do the following:

   1. Under **Deployment information**, enter or modify the friendly name for your deployment\.

   1. Under **Deployment target**, select **Core device** and enter a target name\. 

   1. Choose **Next**\.

1. On the **Select components** page, do the following:

   1. Under **My components**, choose the name of your client application component \(`com.lookoutvison.EdgeAgentPythonExample`\)\.

   1. Choose **Next** 

1. On the **Configure components** page, keep the current configuration and choose **Next**\.

1. On the **Configure advanced settings** page, keep the current settings and choose **Next**\.

1. On the **Review** page, choose **Deploy** to start deploying your component\.

### Checking deployment status \(Console\)<a name="device-deploy-components-status"></a>

You can check the status of your deployment from the AWS IoT Greengrass V2 console\. If your client application component uses the example recipe and code from [Creating the client application component](edge-inference-create-custom-component.md), view the client application component [log](https://docs.aws.amazon.com/greengrass/v2/developerguide/monitor-logs.html) after the deployment completes\. If successful, the log includes a list of the Lookout for Vision models that are deployed to the component\. 

For information about using the AWS SDK to check deployment status, see [Check deployment status](https://docs.aws.amazon.com/greengrass/v2/developerguide/check-deployment-status.html)\.

**To check deployment status**

1. Open the AWS IoT Greengrass console at [https://console\.aws\.amazon\.com/iot/](https://console.aws.amazon.com/greengrass/)

1. On the left navigation pane, choose **Core devices**\.

1. Under **Greengrass core devices** choose your core device\.

1. Choose the **Deployments tab** to view the current deployment status\. 

1. After the deployments succeeds \(status is **Completed**\), open a terminal window on the core device and view the client application component log at `/greengrass/v2/logs/com.lookoutvison.EdgeAgentPythonExample.log`\. If your deployment uses the example recipe and code, the log includes the output from `lookoutvision_edge_agent_example.py`\. For example:

   ```
   Model Details model_component:"ModelComponent"
   ```

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

   1. Run the following command to list the deployments for the deployment target\. Replace `targetArn` with the Amazon Resource Name \(ARN\) of the target AWS IoT thing or thing group\. To get the ARNs of the things in the current AWS Region, use the command `aws iot list-things`\.

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
      + `tags` — The deployment's tags\.

1. Run the following command to deploy the components on the device\. Note the value of the `deploymentId` in the response\.

   ```
   aws greengrassv2 create-deployment \
       --cli-input-json file://path/to/deployment.json
   ```

1. Run the following command to get the status of the deployment\. Change `deployment-id` to the value you noted in the previous step\. The deployment has completed successfuly if the value of `deploymentStatus` is `COMPLETED`\.

   ```
   aws greengrassv2 get-deployment --deployment-id deployment-id
   ```

1. After the deployments succeeds, open a terminal window on the core device and view the client application component log at `/greengrass/v2/logs/com.lookoutvison.EdgeAgentPythonExample.log`\. If your deployment uses the example recipe and code, the log includes the output from `lookoutvision_edge_agent_example.py`\. For example:

   ```
   Model Details model_component:"ModelComponent"
   ```