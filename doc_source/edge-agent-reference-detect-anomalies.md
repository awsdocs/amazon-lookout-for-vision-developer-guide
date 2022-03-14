# DetectAnomalies<a name="edge-agent-reference-detect-anomalies"></a>

Detects anomalies in the supplied image\.

The response from `DetectAnomalies` includes a Boolean prediction that the image contains one or more anomalies and a confidence value for the prediction\. 

**Note**  
The model that you use with `DetectAnomalies` must be running\. You can get the current status by calling [DescribeModel](edge-agent-reference-describe-model.md)\. To start running a model, see [StartModel](edge-agent-reference-start-model.md)\.

`DetectAnomalies` supports packed bitmaps \(images\) in interleaved RGB888 format\. The minimum supported image dimension is 64x64 pixels\. The maximum supported image dimension is 4096x4096 pixels\.

You can send the image in the protobuf message or through a shared memory segment\. Serializing large images into the protobuf message can significantly increase the latency of calls to `DetectAnomalies`\. For the least latency, we recommended that you use shared memory\.

```
rpc DetectAnomalies(DetectAnomaliesRequest) returns (DetectAnomaliesResponse);
```

## DetectAnomaliesRequest<a name="edge-agent-reference-detect-anomalies-request"></a>

```
message Bitmap {
  int32 width = 1;
  int32 height = 2;
  oneof data { 
    bytes byte_data = 3;
    SharedMemoryHandle shared_memory_handle = 4;
  }
}
```

```
message SharedMemoryHandle {
  string name = 1;
  uint64 size = 2;
  uint64 offset = 3;
}
```

```
message DetectAnomaliesRequest {
  string model_component = 1;
  Bitmap bitmap = 2;
}
```

### Bitmap<a name="detect-anomalies-bitmap"></a>

The image that you want to analyze with `DetectAnomalies`\.

**width**  
The width of the image in pixels\.

**height**  
The height of the image in pixels\.

**byte\_data**  
Image bytes passed in protobuf message\.

**shared\_memory\_handle**  
 Image bytes passed in shared memory segment\.

#### SharedMemoryHandle<a name="detect-anomalies-shared-memory-handle"></a>

 Represents a POSIX shared memory segment\.

**name**  
The name of the POSIX memory segment\. For information about creating shared memory, see [shm\_open](https://man7.org/linux/man-pages/man3/shm_open.3.html)\.

**size**  
The image buffer size in bytes starting from the offset\.

**offset**  
The offset, in bytes, to the beginning of the image buffer from the start of the shared memory segment\.

### DetectAnomaliesRequest<a name="edge-agent-reference-detect-anomalies-request-fields"></a>

**model\_component**  
The name of the AWS IoT Greengrass V2 component that contains the model you want to use\.

**bitmap**  
The image that you want analyze with `DetectAnomalies`\.

## DetectAnomaliesResponse<a name="edge-agent-reference-detect-anomalies-response"></a>

```
message DetectAnomalyResult {
  bool is_anomalous = 1;
  float confidence = 2;
}
```

```
message DetectAnomaliesResponse {
  DetectAnomalyResult detect_anomaly_result = 1;
}
```

**is\_anomalous**  
Indicates if the image contains an anomaly\. `true` if the image contains an anomaly\. `false` if the image is normal\.

**confidence**  
The confidence that `DetectAnomalies` has in the accuracy of the prediction\. `confidence` is a floating point value between 0 and 1\.

### Status codes<a name="edge-agent-reference-detect-anomalies-response-status-codes"></a>


| Code | Number | Description | 
| --- | --- | --- | 
|  OK  |  0  |  `DetectAnomalies` successfully made a prediction  | 
|  UNKNOWN  |  2  |  An unknown error has occurred\.  | 
|  INVALID\_ARGUMENT   |  3  |  One or more input parameters are invalid\. Check the error message for more details\.  | 
|  NOT\_FOUND  |  5  |  A model with the specified name wasn't found\.  | 
|  RESOURCE\_EXHAUSTED   |  8  |  There aren't enough resources to perform this operation\. For example, The Lookout for Vision Edge Agent can't keep up with the rate of calls to `DetectAnomalies`\. Check the error message for more details\.   | 
|  FAILED\_PRECONDITION   |  9  |  `DetectAnomalies` was called for model that is not in the RUNNING state\.  | 
|  INTERNAL  |  13  |  An internal error has occurred\.  | 