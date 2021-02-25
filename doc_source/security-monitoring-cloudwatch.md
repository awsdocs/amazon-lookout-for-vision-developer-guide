# Monitoring Lookout for Vision with Amazon CloudWatch<a name="security-monitoring-cloudwatch"></a>

You can use CloudWatch to monitor image processing and anomaly detecting metrics in Lookout for Vision\.

## Lookout for Vision metrics<a name="cloudwatch-metrics"></a>

Amazon Lookout for Vision sends the following metrics to CloudWatch for the `DetectAnomalies` action:


| Metric | Description | 
| --- | --- | 
|  `DetectedAnomalyCount` |  The number of anomalies detected in a project Valid Statistics: `Sum,Average` Unit: Count  | 
|  `ProcessedImageCount` |  The total number of images run through anomaly detection Valid Statistics: `Sum,Average` Unit: Count  | 
|  `InvalidImageCount` |  The number of images that were invalid and could not return a result Valid Statistics: `Sum,Average` Unit: Count  | 
|  `SuccessfulRequestCount` |  The number of successful API calls Valid Statistics: `Sum,Average` Unit: Count  | 
|  `ErrorCount` |  The number of API errors Valid Statistics: `Sum,Average` Unit: Count  | 
|  `ThrottledCount` |  The number of API errors that were due to throttling Valid Statistics: `Sum,Average` Unit: Count  | 
|  `Time` |  The time in milliseconds for Lookout for Vision to compute the anomaly detection Valid Statistics: `Data Samples,Average` Units: Milliseconds for `Average` statistics and Count for `Data Samples` statistics  | 

## Dimensions of Lookout for Vision metrics<a name="cloudwatch-metric-dimensions"></a>

Lookout for Vision metrics use the Lookout for Vision namespace and provide metrics for the following dimensions:


| Dimension | Description | 
| --- | --- | 
|  `ProjectName`  |  You can split metrics by project to see which projects are having problems or need to be updated\.  | 
|  `ModelName`  |  You can split metrics by model to see which models are having problems or need to be updated\.  | 