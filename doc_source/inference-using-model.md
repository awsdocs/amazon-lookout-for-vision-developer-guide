# Using a model in your client application component<a name="inference-using-model"></a>

The steps for using a model from a client application component are similar to using a model hosted in the cloud\.

1. Start running the model\. 

1. Detect anomalies in images\.

1. Stop the model, if no longer needed\.

The Amazon Lookout for Vision Edge Agent provides API to start a model, detect anomalies in an image, and stop a model\. You can also use the API to list the models on a device and get information about a deployed model\. For more information, see [Amazon Lookout for Vision Edge Agent API reference](edge-agent-reference.md)\. 

You can get error information by checking the gRPC status codes\. For more information, see [Getting error information](#client-application-overview-getting-errors)\.

To write your code, you can use any language supported by gRPC\. We provide example Python code\. 

**Topics**
+ [Using the stub in your client application component](#edge-client-application-accessing-stub)
+ [Starting the model](#client-application-overview-start-model)
+ [Detecting anomalies](#client-application-overview-detect-anomalies)
+ [Stopping the model](#client-application-overview-stop-model)
+ [Listing models on a device](#client-application-overview-list-models)
+ [Describing a model](#client-application-overview-describing-model)
+ [Getting error information](#client-application-overview-getting-errors)

## Using the stub in your client application component<a name="edge-client-application-accessing-stub"></a>

Use the following code to set up access to your model through the Lookout for Vision Edge Agent\.

```
import grpc
from edge_agent_pb2_grpc import EdgeAgentStub
import edge_agent_pb2 as pb2

# Creating stub.
 with grpc.insecure_channel("unix:///tmp/aws.iot.lookoutvision.EdgeAgent.sock") as channel:
    stub = EdgeAgentStub(channel)
    # Add additional code that works with Edge Agent in this block to prevent resources leakage
```

## Starting the model<a name="client-application-overview-start-model"></a>

 You start a model by calling the [StartModel](edge-agent-reference-start-model.md) API\. The model might take a while to start\. You can check the current status by calling [DescribeModel](edge-agent-reference-describe-model.md)\. The model is running if the value of the `status` field is Running\.

### Example code<a name="client-application-overview-start-model-example"></a>

Replace *component\_name* with the name of your model component\.

```
import time

import grpc
from edge_agent_pb2_grpc import EdgeAgentStub
import edge_agent_pb2 as pb2


model_component_name = "component_name"


def start_model_if_needed(stub, model_name):
    # Starting model if needed.
    while True:
        model_description_response = stub.DescribeModel(pb2.DescribeModelRequest(model_component=model_name))
        if model_description_response.model_description.status == pb2.RUNNING:
            print("Model is already running.")
            break
        elif model_description_response.model_description.status == pb2.STOPPED:
            print("Starting the model.")
            stub.StartModel(pb2.StartModelRequest(model_component=model_name))
            continue
        print(f"Waiting for model to start.")
        if model_description_response.model_description.status != pb2.STARTING:
            break
        time.sleep(1.0)


# Creating stub.
with grpc.insecure_channel("unix:///tmp/aws.iot.lookoutvision.EdgeAgent.sock") as channel:
    stub = EdgeAgentStub(channel)
    start_model_if_needed(stub, model_component_name)
```

## Detecting anomalies<a name="client-application-overview-detect-anomalies"></a>

You use the [DetectAnomalies](edge-agent-reference-detect-anomalies.md) API to detect anomalies in an image\. 

The `DetectAnomalies` operation expects the image bitmap to be passed in RGB888 packed format\. The first byte represents the red channel, the second byte represents the green channel, and the third byte represents the blue channel\. If you provide the image in a different format, such as BGR, the predictions from DetectAnomalies are incorrect\.

By default, OpenCV uses the BGR format for image bitmaps\. If you are using OpenCV to capture images for analysis with `DetectAnomalies`, you must convert the image to RGB888 format before you pass the image to `DetectAnomalies`\.

### Detecting Anomalies by using image bytes<a name="client-application-overview-detect-anomalies-image-bytes"></a>

You can detect anomalies in an image by supplying the image as image bytes\. In the following example, the image bytes are retrieved from an image stored in the local file system\. 

Replace *sample\.jpg* with the name of the image file that you want to analyze\. Replace *component\_name* with the name of your model component\.

```
import time

from PIL import Image
import grpc
from edge_agent_pb2_grpc import EdgeAgentStub
import edge_agent_pb2 as pb2


model_component_name = "component_name"

....
# Detecting anomalies.
def detect_anomalies(stub, model_name, image_path):
    image = Image.open(image_path)
    image = image.convert("RGB")
    detect_anomalies_response = stub.DetectAnomalies(
        pb2.DetectAnomaliesRequest(
            model_component=model_name,
            bitmap=pb2.Bitmap(
                width=image.size[0]
                height=image.size[1]
                byte_data=bytes(image.tobytes())
            ) 
        )
    )
    print(f"Image is anomalous - {detect_anomalies_response.detect_anomaly_result.is_anomalous}")
    return detect_anomalies_response.detect_anomaly_result


# Creating stub.
with grpc.insecure_channel("unix:///tmp/aws.iot.lookoutvision.EdgeAgent.sock") as channel:
    stub = EdgeAgentStub(channel)
    start_model_if_needed(stub, model_component_name)
    detect_anomalies(stub, model_component_name, "sample.jpg")
```

### Detecting Anomalies by using shared memory segment<a name="client-application-overview-detect-anomalies-shared-memory"></a>

You can detect anomalies in an image by supplying the image as image bytes in the POSIX shared memory segment\. For best performance, we recommend using shared memory for DetectAnomalies requests\. For more information, see [DetectAnomalies](edge-agent-reference-detect-anomalies.md)\.

## Stopping the model<a name="client-application-overview-stop-model"></a>

If you are no longer using the model, the [StopModel](edge-agent-reference-stop-model.md) API to stop the model running\.

```
stop_model_response = stub.StopModel(
    pb2.StopModelRequest(
         model_component=model_component_name   
    )
)
print(f"New status of the model is {stop_model_response.status}")
```

## Listing models on a device<a name="client-application-overview-list-models"></a>

You can use the [ListModels](edge-agent-reference-list-models.md) API to list the models that are deployed to a device\.

```
models_list_response = stub.ListModels(
    pb2.ListModelsRequest()
)
for model in models_list_response.models:
    print(f"Model Details {model}")
```

## Describing a model<a name="client-application-overview-describing-model"></a>

You can get information about a model that's deployed to a device by calling the [DescribeModel](edge-agent-reference-describe-model.md) API\. Using `DescribeModel` is useful for getting the current status of a model\. For example, you need to know if a model is running before you can call `DetectAnomalies`\. For example code, see [Starting the model](#client-application-overview-start-model)\. 

## Getting error information<a name="client-application-overview-getting-errors"></a>

gRPC status codes are used to report API results\. 

You can get error information by catching the `RpcError` exception, as shown in the following example\. For information about the error status codes, see the [reference topic](edge-agent-reference.md) for an API\. 

```
# Error handling.
try:
    stub.DetectAnomalies(detect_anomalies_request)
except grpc.RpcError as e:
    print(f"Error code: {e.code()}, Status: {e.details()}")
```