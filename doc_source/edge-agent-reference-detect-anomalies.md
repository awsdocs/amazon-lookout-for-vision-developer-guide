# DetectAnomalies<a name="edge-agent-reference-detect-anomalies"></a>

Detects anomalies in the supplied image\.

The response from `DetectAnomalies` includes a Boolean prediction that the image contains one or more anomalies and a confidence value for the prediction\. If the model is a segmentation model, the response includes the following:
+ A mask image that covers each anomaly type in a unique color\. You can have `DetectAnomalies` store the mask image in shared memory, or return the mask as image bytes\. 
+ The percentage area of the image that an anomaly type covers\.
+ The hex color for an anomaly type on the mask image\.

**Note**  
The model that you use with `DetectAnomalies` must be running\. You can get the current status by calling [DescribeModel](edge-agent-reference-describe-model.md)\. To start running a model, see [StartModel](edge-agent-reference-start-model.md)\.

`DetectAnomalies` supports packed bitmaps \(images\) in interleaved RGB888 format\. The first byte represents the red channel, the second byte represents the green channel, and the third byte represents the blue channel\. If you provide the image in a different format, such as BGR, the predictions from DetectAnomalies are incorrect\.

By default, OpenCV uses the BGR format for image bitmaps\. If you are using OpenCV to capture images for analysis with `DetectAnomalies`, you must convert the image to RGB888 format before you pass the image to `DetectAnomalies`\.

The minimum supported image dimension is 64x64 pixels\. The maximum supported image dimension is 4096x4096 pixels\.

You can send the image in the protobuf message or through a shared memory segment\. Serializing large images into the protobuf message can significantly increase the latency of calls to `DetectAnomalies`\. For the least latency, we recommended that you use shared memory\.

```
rpc DetectAnomalies(DetectAnomaliesRequest) returns (DetectAnomaliesResponse);
```

## DetectAnomaliesRequest<a name="edge-agent-reference-detect-anomalies-request"></a>

The input parameters for `DetectAnomalies`\.

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
message AnomalyMaskParams {
SharedMemoryHandle shared_memory_handle = 2;
}
```

```
message DetectAnomaliesRequest {
string model_component = 1;
Bitmap bitmap = 2;
AnomalyMaskParams anomaly_mask_params = 3;
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

### AnomalyMaskParams<a name="edge-agent-reference-detection-anomalies-anomaly-mask-params"></a>

Parameters for outputting an anomaly mask\. \(Segmentation model\)\.

**shared\_memory\_handle**  
Contains the image bytes for the mask, if `shared_memory_handle` wasn't provided\. 

### DetectAnomaliesRequest<a name="edge-agent-reference-detect-anomalies-request-fields"></a>

**model\_component**  
The name of the AWS IoT Greengrass V2 component that contains the model you want to use\.

**bitmap**  
The image that you want analyze with `DetectAnomalies`\.

**anomaly\_mask\_params**  
Optional parameters for outputting the mask\. \(Segmentation model\)\.

## DetectAnomaliesResponse<a name="edge-agent-reference-detect-anomalies-response"></a>

The response from `DetectAnomalies`\.

```
message DetectAnomalyResult {
bool is_anomalous = 1;
float confidence = 2;
Bitmap anomaly_mask = 3;
repeated Anomaly anomalies = 4;
}
```

```
message Anomaly {
string name = 1;
PixelAnomaly pixel_anomaly = 2;
```

```
message PixelAnomaly {
float total_percentage_area = 1;
string hex_color = 2;
}
```

```
message DetectAnomaliesResponse {
  DetectAnomalyResult detect_anomaly_result = 1;
}
```

### Anomaly<a name="edge-agent-reference-detect-anomalies-response-detect-anomaly-result-anomaly"></a>

Represents an anomaly found on an image\. \(Segmentation model\)\.

**name**  
The name of an anomaly type found in an image\. `name` maps to an anomaly type in the training dataset\. The service automatically inserts the background anomaly type into the response from DetectAnomalies\. 

**pixel\_anomaly**  
Information about the pixel mask that covers an anomaly type\.

### PixelAnomaly<a name="edge-agent-reference-detect-anomalies-response-detect-anomaly-result-anomaly-pixel_anomaly"></a>

Information about the pixel mask that covers an anomaly type\. \(Segmentation model\)\.

**total\_percentage\_area**  
The percentage area of the image that the anomaly type covers\.

**hex\_color**  
A hex color value that represents the anomaly type on the image\. The color maps to the color of the anomaly type used in the training dataset\. 

### DetectAnomalyResult<a name="edge-agent-reference-detect-anomalies-response-detect-anomaly-result"></a>

**is\_anomalous**  
Indicates if the image contains an anomaly\. `true` if the image contains an anomaly\. `false` if the image is normal\.

**confidence**  
The confidence that `DetectAnomalies` has in the accuracy of the prediction\. `confidence` is a floating point value between 0 and 1\.

**anomaly\_mask**  
if shared\_memory\_handle wasn't provided, contains the image bytes for the mask\. \(Segmentation model\)\.

**Anomalies**  
A list of 0 or more anomalies found within the input image\. \(Segmentation model\)\. 

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