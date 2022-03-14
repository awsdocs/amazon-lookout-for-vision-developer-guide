# ListModels<a name="edge-agent-reference-list-models"></a>

Lists the models deployed to an AWS IoT Greengrass Version 2 core device\. 

```
rpc ListModels(ListModelsRequest) returns (ListModelsResponse);
```

## ListModelsRequest<a name="edge-agent-reference-list-models-request"></a>

```
message ListModelsRequest {}
```

## ListModelsResponse<a name="edge-agent-reference-list-models-response"></a>

```
message ModelMetadata {
  string model_component = 1;
  string lookout_vision_model_arn = 2;
  ModelStatus status = 3;
  string status_message = 4;
}
```

```
message ListModelsResponse {
  repeated ModelMetadata models = 1;
}
```

### ModelMetadata<a name="model-metadata"></a>

**model\_component**  
The name of AWS IoT Greengrass Version 2 component that contains an Amazon Lookout for Vision model\.

**lookout\_vision\_model\_arn**  
The Amazon Resource Name \(ARN\) of the Amazon Lookout for Vision model that was used to generate the AWS IoT Greengrass V2 component\.

**status**  
The current status of the model\. For more information, see [ModelStatus](edge-agent-reference-enums-model-status.md)\.

**status\_message**  
The status message for the model\.

## Status codes<a name="edge-agent-reference-start-model-response-status-codes"></a>


| Code | Number | Description | 
| --- | --- | --- | 
|  OK  |  0  |  The call was successful\.  | 
|  UNKNOWN  |  2  |  An unknown error has occurred\.  | 
|  INTERNAL  |  13  |  An internal error has occurred\.  | 