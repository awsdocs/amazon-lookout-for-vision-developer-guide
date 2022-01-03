# StartModel<a name="edge-agent-reference-start-model"></a>


|  | 
| --- |
| The model packaging feature is in preview release for Amazon Lookout for Vision and is subject to change\. | 

Starts a model running on an AWS IoT Greengrass Version 2 core device\. It might take a while for the model to start running\. To check the current status call [DescribeModel](edge-agent-reference-describe-model.md)\. The model is running if the `Status` field is `RUNNING`\.

The number of models that you can run concurrently depends on the hardware specification of your core device\. 

```
rpc StartModel(StartModelRequest) returns (StartModelResponse);
```

## StartModelRequest<a name="edge-agent-reference-start-model-request"></a>

```
message StartModelRequest {
  string model_component = 1;
}
```

**model\_component**  
The name of the AWS IoT Greengrass Version 2 component that contains the model you want to start\.

## StartModelResponse<a name="edge-agent-reference-start-model-response"></a>

```
message StartModelResponse {
  ModelStatus status = 1;
}
```

**status**  
The current status of the model\. The response is `STARTING` if the call succeeds\. For more information, see [ModelStatus](edge-agent-reference-enums-model-status.md)\.

## Status codes<a name="edge-agent-reference-start-model-response-status-codes"></a>


| Code | Number | Description | 
| --- | --- | --- | 
|  OK  |  0  |  The model is starting  | 
|  UNKNOWN  |  2  |  An unknown error has occurred\.  | 
|  INVALID\_ARGUMENT   |  3  |  One or more input parameters are invalid\. Check the error message for more details\.  | 
|  NOT\_FOUND  |  5  |  A model with the supplied name wasn't found\.  | 
|  RESOURCE\_EXHAUSTED   |  8  |  There isn't enough resources to perform this operation\. For example, there is isn't enough memory to load the model\. Check the error message for more details\.  | 
|  FAILED\_PRECONDITION   |  9  |  The method was called for model that is not in the STOPPED or FAILED state\.  | 
|  INTERNAL  |  13  |  An internal error has occurred\.  | 