# DescribeModel<a name="edge-agent-reference-describe-model"></a>

Describes an Amazon Lookout for Vision model that's deployed to an AWS IoT Greengrass Version 2 core device\. 

```
rpc DescribeModel(DescribeModelRequest) returns (DescribeModelResponse);
```

## DescribeModelRequest<a name="edge-agent-reference-describe-model-request"></a>

```
message DescribeModelRequest {
  string model_component = 1;
}
```

**model\_component**  
The name of the AWS IoT Greengrass V2 component that contains the model you want to describe\.

## DescribeModelResponse<a name="edge-agent-reference-describe-model-response"></a>

```
message ModelDescription {
  string model_component = 1;
  string lookout_vision_model_arn = 2;
  ModelStatus status = 3;
  string status_message = 4;
}
```

```
message DescribeModelResponse {
  ModelDescription model_description = 1;
}
```

### ModelDescription<a name="model-description"></a>

**model\_component**  
The name of AWS IoT Greengrass Version 2 component that contains the Amazon Lookout for Vision model\.

**lookout\_vision\_model\_arn**  
The Amazon Resource Name ARN of the Amazon Lookout for Vision model that was used to generate the AWS IoT Greengrass V2 component\.

**status**  
The current status of the model\. For more information, see [ModelStatus](edge-agent-reference-enums-model-status.md)\.

**status\_message**  
The status message for the model\. 

## Status codes<a name="edge-agent-reference-describe-model-response-status-codes"></a>


| Code | Number | Description | 
| --- | --- | --- | 
|  OK  |  0  |  The call was successful\.  | 
|  UNKNOWN  |  2  |  An unknown error has occurred\.  | 
|  INVALID\_ARGUMENT   |  3  |  One or more input parameters are invalid\. Check the error message for more details\.  | 
|  NOT\_FOUND  |  5  |  A model with the supplied name wasn't found\.  | 
|  INTERNAL  |  13  |  An internal error has occurred\.  | 