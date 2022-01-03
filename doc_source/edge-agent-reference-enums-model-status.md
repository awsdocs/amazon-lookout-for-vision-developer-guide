# ModelStatus<a name="edge-agent-reference-enums-model-status"></a>


|  | 
| --- |
| The model packaging feature is in preview release for Amazon Lookout for Vision and is subject to change\. | 

The status of a model that's deployed to an AWS IoT Greengrass Version 2 core device\. To get the current status, call [DescribeModel](edge-agent-reference-describe-model.md)\.

```
enum ModelStatus {
  STOPPED = 0;
  STARTING = 1;
  RUNNING = 2;
  FAILED = 3;
  STOPPING = 4;
}
```