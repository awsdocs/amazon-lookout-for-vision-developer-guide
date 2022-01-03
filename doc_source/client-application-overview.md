# Writing your client application component<a name="client-application-overview"></a>


|  | 
| --- |
| The model packaging feature is in preview release for Amazon Lookout for Vision and is subject to change\. | 

A client application component is a custom AWS IoT Greengrass Version 2 component that you write\. It implements the business logic you need to use an Amazon Lookout for Vision model on an AWS IoT Greengrass Version 2 core device\.

To access a model, your client application component uses the Lookout for Vision Edge Agent component\. The Lookout for Vision Edge Agent component provides an API that you use to analyze images with a model and manage the models on a core device\.

The Lookout for Vision Edge Agent API is implemented using gRPC, which is a protocol for making remote procedure calls\. For more information, see [gRPC](https://grpc.io/)\. To write your code, you can use any language supported by gRPC\. We provide example Python code\. For more information, see [Using a model in your client application component](inference-using-model.md)\.



**Note**  
The Lookout for Vision Edge Agent component is a dependency of the model component that you deploy\. It is automatically deployed to the core device when you deploy the model component to the core device\. 

To write a client application component, you do the following\.

1. [Set up your environment](edge-client-application-setup-environment.md) to use gRPC and install third\-party libraries\. 

1. [Write code to use the model](inference-using-model.md)\.

1. [Deploy the code as a custom component](device-deploy-components.md) to the core device\.

For an example client application component that shows how to perform anomaly detection in a custom GStreamer pipeline, see [https://github\.com/awslabs/aws\-greengrass\-labs\-lookoutvision\-gstreamer](https://github.com/awslabs/aws-greengrass-labs-lookoutvision-gstreamer)\.