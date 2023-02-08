# Monitoring Lookout for Vision with Amazon CloudWatch<a name="monitoring-cloudwatch"></a>

You can monitor Lookout for Vision using CloudWatch, which collects raw data and processes it into readable, near real\-time metrics\. These statistics are kept for 15 months, so that you can access historical information and gain a better perspective on how your web application or service is performing\. You can also set alarms that watch for certain thresholds, and send notifications or take actions when those thresholds are met\. For more information, see the [Amazon CloudWatch User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/)\.

The Lookout for Vision service reports the following metrics in the `AWS/LookoutVision` namespace\.


| Metric | Description | 
| --- | --- | 
|  `DetectedAnomalyCount` |  The number of anomalies detected in a project Valid Statistics: `Sum,Average` Unit: Count  | 
|  `ProcessedImageCount` |  The total number of images run through anomaly detection Valid Statistics: `Sum,Average` Unit: Count  | 
|  `InvalidImageCount` |  The number of images that were invalid and could not return a result Valid Statistics: `Sum,Average` Unit: Count  | 
|  `SuccessfulRequestCount` |  The number of successful API calls Valid Statistics: `Sum,Average` Unit: Count  | 
|  `ErrorCount` |  The number of API errors Valid Statistics: `Sum,Average` Unit: Count  | 
|  `ThrottledCount` |  The number of API errors that were due to throttling Valid Statistics: `Sum,Average` Unit: Count  | 
|  `Time` |  The time in milliseconds for Lookout for Vision to compute the anomaly detection Valid Statistics: `Data Samples,Average` Units: Milliseconds for `Average` statistics and Count for `Data Samples` statistics  | 
| `MinInferenceUnits` | The minimum number of inference units specified during the `StartModel` request\. Valid statistics: `Average` Unit: Count  | 
| `MaxInferenceUnits` | The maximum number of inference units specified during the `StartModel`request\. Valid statistics: `Average` Unit: Count  | 
|  `DesiredInferenceUnits`  |  The number of inference units to which Lookout for Vision is scaling up or down\.  Valid statistics: `Average` Unit: Count  | 
|  `InServiceInferenceUnits`  |  The number of inference units that the model is using\. Valid statistics: `Average` It is recommended that you use the Average statistic to obtain the 1 minute average of how many instances are used\. Unit: Count  | 

The following dimensions are supported for the Lookout for Vision metrics\.


| Dimension | Description | 
| --- | --- | 
|  `ProjectName`  |  You can split metrics by project to see which projects are having problems or need to be updated\.  | 
|  `ModelVersion`  |  You can split metrics by model version to see which models are having problems or need to be updated\.  | 