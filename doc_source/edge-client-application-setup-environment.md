# Setting up your environment<a name="edge-client-application-setup-environment"></a>


|  | 
| --- |
| The model packaging feature is in preview release for Amazon Lookout for Vision and is subject to change\. | 

Your client code should use gRPC client to access the Amazon Lookout for Vision Edge Agent\. This section shows how to set up your environment with gRPC and install third\-party dependencies needed for the `DetectAnomalies` example code\. 

**Topics**
+ [Setting up gRPC](#edge-client-application-create-stub)
+ [Adding third\-party dependencies](#edge-client-application-3rd-party)

## Setting up gRPC<a name="edge-client-application-create-stub"></a>

 You need a gRPC client that you use in your code to call the Lookout for Vision Edge Agent API\. To do this, you create a gRPC stub by using the `.proto` service definition file available in the Lookout for Vision Edge Agent application bundle\. The application bundle is installed when the Lookout for Vision Edge Agent component is installed as a dependency of the model component\. The application bundle is located at `/greengrass/v2/packages/artifacts-unarchived/aws.iot.lookoutvision.EdgeAgent/edge_agent_version/lookoutvision_edge_agent`\. Replace `edge_agent_version` with version of the Lookout for Vision Edge Agent that you are using\.

**To set up gRPC**

1. Copy the file `edge-agent.proto` from `/greengrass/v2/packages/artifacts-unarchived/aws.iot.lookoutvision.EdgeAgent/edge_agent_version/lookoutvision_edge_agent/protos/edge-agent.proto` to your working directory\. 

1. Use the following commands to generate the Python client interfaces from the `.proto` service definition for the Lookout for Vision Edge Agent\.

   ```
   %%bash
   python3 -m pip install grpcio
   python3 -m pip install grpcio-tools
   python3 -m grpc_tools.protoc --proto_path=. --python_out=. --grpc_python_out=. edge-agent.proto
   ```

   If the commands are successful, the stubs `edge_agent_pb2_grpc.py` and `edge_agent_pb2.py` are created in the working directory\.

1. Write the client code that uses your model\. For more information, see [Using a model in your client application component](inference-using-model.md)\.

## Adding third\-party dependencies<a name="edge-client-application-3rd-party"></a>

The `DetectAnomalies` example code uses the [Pillow](https://python-pillow.org/) library to work with images\. For more information, see [Detecting Anomalies by using image bytes](inference-using-model.md#client-application-overview-detect-anomalies-image-bytes)\.

Use the following command to install the Pillow library\. 

```
python3 -m pip install Pillow
```