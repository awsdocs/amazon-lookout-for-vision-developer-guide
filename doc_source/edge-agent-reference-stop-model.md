# StopModel<a name="edge-agent-reference-stop-model"></a>


|  | 
| --- |
| The model packaging feature is in preview release for Amazon Lookout for Vision and is subject to change\. | 

Stops a model running on an AWS IoT Greengrass Version 2 core device\. `StopModel` returns after the model has stopped\. The model has stopped successfully if the `Status` field in the response is `STOPPED`\.

```
  rpc StopModel(StopModelRequest) returns (StopModelResponse);
```

## StopModelRequest<a name="edge-agent-reference-stop-model-request"></a>

```
message StopModelRequest {
  string model_component = 1;
}
```

**model\_component**  
The name of the AWS IoT Greengrass Version 2 component that contains the model you want to stop\.

## StopModelResponse<a name="edge-agent-reference-stop-model-response"></a>

```
message StopModelResponse {
  ModelStatus status = 1;
}
```

**status**  
The current status of the model\. The response is `STOPPED` if the call succeeds\. For more information, see [ModelStatus](edge-agent-reference-enums-model-status.md)\.

## Status codes<a name="edge-agent-reference-stop-model-response-status-codes"></a>


| Code | Number | Description | 
| --- | --- | --- | 
|  OK  |  0  |  The model is stopping\.  | 
|  UNKNOWN  |  2  |  An unknown error has occurred\.  | 
|  INVALID\_ARGUMENT   |  3  |  One or more input parameters are invalid\. Check the error message for more details\.  | 
|  NOT\_FOUND  |  5  |  A model with the supplied name wasn't found\.  | 
|  FAILED\_PRECONDITION   |  9  |  The method was called for a model that is not in the RUNNING state\.  | 
|  INTERNAL  |  13  |  An internal error has occurred\.  | 