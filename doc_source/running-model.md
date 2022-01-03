# Running your trained Amazon Lookout for Vision model<a name="running-model"></a>

To detect anomalies in images with your model, you must first start your model with the [StartModel](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_StartModel) operation\.

After your model starts, you can use the `DetectAnomalies` operation to detect anomalies in an image\. For more information, see [Detecting anomalies in an image](inference-detect-anomalies.md)\.  

When you start your model, Amazon Lookout for Vision provisions a minimum of one compute resource, known as an inference unit\. You specify the number of inference units to use in the `MinInferenceUnits` input parameter to the `StartModel` API\. The default allocation for a model is 1 inference unit\. 

You can increase or decrease the throughput of your model depending on the demands of your manufacturing line\. To increase capacity, use additional inference units\. Each additional inference unit increases your processing speed by one inference unit\.

The transactions per second \(TPS\) that a single inference unit supports is affected by the following:
+ The algorithm that Lookout for Vision uses to train the model\. When you train a model, multiple models are trained\. Lookout for Vision selects the model with the best performance based on the size of the dataset and its composition of normal and anomalous images\. 
+ Higher resolution images require more time for analysis\.
+ Smaller sized images \(measured in MBs\) are analyzed faster than larger images\.

Amazon Lookout for Vision distributes inference units across multiple Availability Zones within an AWS Region to provide increased availability\. For more information, see [Availability Zones](https://aws.amazon.com/about-aws/global-infrastructure/regions_az/#Availability_Zones)\. To help protect your production models from Availability Zone outages and inference unit failures, we strongly recommend that you start your production models with at least 2 inference units\. 

If an Availability Zone outage occurs, all inference units in the Availability Zone are unavailable and model capacity is reduced\. Calls to [DetectAnomalies](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_DetectAnomalies) are redistributed across the remaining inference units and succeed if the calls don’t exceed the supported Transactions Per Seconds \(TPS\) of the remaining inference units\. After AWS repairs the Availability Zone, the inference units are restarted, and full capacity is restored\. 

If a single inference unit fails, Lookout for Vision automatically starts a new inference unit in the same Availability Zone\. Model capacity is reduced until the new inference unit starts\.

You are charged for the number of hours that your model is running and for the number of inference units that your model uses whilst it is running\. For example, if you start the model with 2 inference units and you use the model for 8 hours, you are charged for 16 inference hours \(8 hours running time \* 2 inference units\)\. 

**Warning**  
If you don't explicitly stop your model by calling [StopModel](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_StopModel), you are charged even if you are not actively detecting anomalies with your model\.

The Amazon Lookout for Vision console provides AWS CLI commands that you can use to start and stop your model\. This section includes example Python code that you can use\. 



**Topics**
+ [Starting your Amazon Lookout for Vision model](run-start-model.md)
+ [Stopping your Amazon Lookout for Vision model](run-stop-model.md)