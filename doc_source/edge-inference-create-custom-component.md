# Creating the client application component<a name="edge-inference-create-custom-component"></a>


|  | 
| --- |
| The model packaging feature is in preview release for Amazon Lookout for Vision and is subject to change\. | 

You can create the client application component once you have generated your gRPC stubs and you have your client application code ready\. The component you create is a custom component that you deploy to an AWS IoT Greengrass Version 2 core device with AWS IoT Greengrass V2\. For more information about component recipes, see [AWS IoT Greengrass Version 2 component recipe reference](https://docs.aws.amazon.com/greengrass/v2/developerguide/component-recipe-reference.html)\. 

**To create the client application component**

1. Save the client application code as `lookoutvision_edge_agent_example.py`

1. Upload `lookoutvision_edge_agent_example.py`, `edge_agent_pb2_grpc.py and edge_agent_pb2.py` to your Amazon S3 bucket and note their Amazon S3 paths\. You created `edge_agent_pb2_grpc.py` and `edge_agent_pb2.py` in [Setting up gRPC](edge-client-application-setup-environment.md#edge-client-application-create-stub)\.

1. Create a custom component in the AWS IoT Greengrass V2 console and define the recipe for your component\. For more information, see [https://docs.aws.amazon.com/greengrass/v2/developerguide/create-components.html](https://docs.aws.amazon.com/greengrass/v2/developerguide/create-components.html) and [https://docs.aws.amazon.com/greengrass/v2/developerguide/publish-components.html](https://docs.aws.amazon.com/greengrass/v2/developerguide/publish-components.html)\. Use the following recipe and change the following values\.
   + `model_component` to the name of your model component\. 
   + Change the URI entries to the S3 paths of `lookoutvision_edge_agent_example.py`, `edge_agent_pb2_grpc.py`, and `edge_agent_pb2.py`\. 

------
#### [ JSON ]

   ```
   {
     "RecipeFormatVersion": "2020-01-25",
     "ComponentName": "com.lookoutvison.EdgeAgentPythonExample",
     "ComponentVersion": "1.0.0",
     "ComponentType": "aws.greengrass.generic",
     "ComponentDescription": "Lookout for Vision Edge Agent Sample Application",
     "ComponentPublisher": "Sample App Publisher",
     "ComponentDependencies": {
       "model_component": {
         "VersionRequirement": ">=1.0.0",
         "DependencyType": "HARD"
       }
     },
     "Manifests": [
       {
         "Platform": {
           "os": "linux",
           "architecture": "/amd64|x86/"
         },
         "Lifecycle": {
           "install": "pip3 install grpcio grpcio-tools protobuf Pillow",
           "run": {
             "script": "python3 {artifacts:path}/lookout_vision_agent_example.py"
           }
         },
         "Artifacts": [
           {
             "Uri": "S3 path to lookoutvision_edge_agent_example.py"
           },
           {
             "Uri": "S3 path to edge_agent_pb2_grpc.py"
           },
           {
             "Uri": "S3 path to edge_agent_pb2.py"
           }
         ]
       }
     ],
     "Lifecycle": {}
   }
   ```

------
#### [ YAML ]

   ```
   ---
   RecipeFormatVersion: 2020-01-25
   ComponentName: com.lookoutvison.EdgeAgentPythonExample
   ComponentVersion: 1.0.0
   ComponentDescription: Lookout for Vision Edge Agent Sample Application
   ComponentPublisher: Sample App Publisher
   ComponentDependencies:
     model_component:
       VersionRequirement: '>=1.0.0'
       DependencyType: HARD
   Manifests:
     - Platform:
         os: linux
         architecture: "/amd64|x86/"
       Lifecycle:
         install: |-
           pip3 install grpcio
           pip3 install grpcio-tools
           pip3 install protobuf
           pip3 install Pillow
         run:
           script: |- 
             python3 {artifacts:path}/lookout_vision_agent_example.py
       Artifacts:
         - URI: S3 path to lookoutvision_edge_agent_example.py
         - URI: S3 path to edge_agent_pb2_grpc.py
         - URI: S3 path to edge_agent_pb2.py
   ```

------

 For detailed information about creating a recipe, see [Create your first component ](https://docs.aws.amazon.com/greengrass/v2/developerguide/getting-started.html#create-first-component) in the *AWS IoT Greengrass V2* documentation\. 