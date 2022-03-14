# Using your Amazon Lookout for Vision model on an edge device<a name="models-devices"></a>

You can use your Amazon Lookout for Vision model on edge devices managed by AWS IoT Greengrass Version 2\. AWS IoT Greengrass is an open source Internet of Things \(IoT\) edge runtime and cloud service\. You can use it to build, deploy, and manage IoT applications on your devices\. For more information, see [AWS IoT Greengrass](https://aws.amazon.com/greengrass)\.

You deploy the same Amazon Lookout for Vision models that you've trained in the cloud onto AWS IoT Greengrass V2 compatible edge devices\. You can then use your deployed model to perform anomaly detection on premises, such as a factory floor, without continually streaming data to the cloud\. That way you can minimize bandwidth costs and detect anomalies locally with real\-time image analysis\. 

**Tip**  
Before deploying a Lookout for Vision model with AWS IoT Greengrass, we recommend that you read the *AWS IoT Greengrass Version 2 developer guide*\. For more information, see [What is AWS IoT Greengrass?](https://docs.aws.amazon.com/greengrass/v2/developerguide/what-is-iot-greengrass.html)\.

To use a Lookout for Vision model on an AWS IoT Greengrass V2 core device, you deploy the model and supporting software as components to the core device\. A component is a software module, such as a Lookout for Vision model, that runs on a Greengrass core device\. There are two forms of component\. A custom component is a component that you create and is only accessible to you\. It is also known as a private component\. An AWS supplied component is a pre\-built component that AWS provides\. It is also known as a public component\. For more information, see [https://docs.aws.amazon.com/greengrass/v2/developerguide/public-components.html](https://docs.aws.amazon.com/greengrass/v2/developerguide/public-components.html)\.

The components that you deploy to a core device for a Lookout for Vision model and supporting software are:
+ *Model component*\. A custom component that contains your Lookout for Vision model\. To create the model component, you use Lookout for Vision to create a *model packaging job*\. A model packaging job creates a component for the model and makes it available as a custom component within AWS IoT Greengrass V2\. For more information, see [Packaging your Amazon Lookout for Vision model](package-job.md)\.
+ *Client application component*\. A custom component you create that implements the code for your business requirements\. For example, finding anomalous circuit boards from images taken after assembly\. For more information, see [Writing your client application component](client-application-overview.md)\.
+ *Amazon Lookout for Vision Edge Agent component*\. An AWS supplied component that provides an API for using and managing your model\. For example, code in your client application component can use the `DetectAnomalies` API to detect anomalies in images\. The Lookout for Vision Edge Agent component is a dependency of the model component\. It is automatically installed on the core device when you deploy the model component\. For more information, see [Amazon Lookout for Vision Edge Agent API reference](edge-agent-reference.md)\. 

After you create the model component and client application component, you can use AWS IoT Greengrass V2 to deploy the components and dependencies to the core device\. For more information, see [Deploying your components to a device](device-deploy-components.md)\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/lookout-for-vision/latest/developer-guide/images/lfv-mpj.png)

**Important**  
The predictions that your model makes with `DetectAnomalies` on a core device might differ from predictions made using the same model hosted in the cloud\. We recommend that you test your model on a core device before using it in a production environment\.    
To reduce prediction mismatches between device hosted models and cloud hosted models, we recommend increasing the number of normal and anomalous images in your training dataset\. We don't recommend reusing existing images to increase the size of the training dataset\.

## Deploying a model and client application component to a AWS IoT Greengrass Version 2 core device<a name="models-devices-using-model"></a>

The procedure for deploying an Amazon Lookout for Vision model and client application component on an AWS IoT Greengrass Version 2 core device is as follows:

1. [Set up your core devices](models-devices-setup-core-device.md) with AWS IoT Greengrass Version 2\. 

1. [Create a model packaging job](package-job.md) by using Lookout for Vision\. The job creates your model component\. 

1. [Write a client application component](client-application-overview.md)\. The component implements your business logic\.

1. [Deploy the model component and client application component](device-deploy-components.md) to the core device by using AWS IoT Greengrass V2\.

After the components and dependencies are deployed to the core device, you can use the model on the core device\. 

**Note**  
You must use the same AWS Region and AWS account to create and deploy your Lookout for Vision model and client application component\. 