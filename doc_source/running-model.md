# Running your trained Amazon Lookout for Vision model<a name="running-model"></a>

To detect anomalies in images with your model, you must first start your model with the [StartModel](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_StartModel) operation\. The Amazon Lookout for Vision console provides AWS CLI commands that you can use to start and stop your model\. This section includes example code that you can use\. 

After your model starts, you can use the `DetectAnomalies` operation to detect anomalies in an image\. For more information, see [Detecting anomalies in an image](inference-detect-anomalies.md)\.

**Topics**
+ [Inference units](#running-model-inference-units)
+ [Availability Zones](#running-model-availability-zones)
+ [Starting your Amazon Lookout for Vision model](run-start-model.md)
+ [Stopping your Amazon Lookout for Vision model](run-stop-model.md)

## Inference units<a name="running-model-inference-units"></a>

When you start your model, Amazon Lookout for Vision provisions a minimum of one compute resource, known as an inference unit\. You specify the number of inference units to use in the `MinInferenceUnits` input parameter to the `StartModel` API\. The default allocation for a model is 1 inference unit\. 

**Important**  
You are charged for the number of hours that your model is running and for the number of inference units that your model uses while it's running, based on how you configure the running of your model\. For example, if you start the model with two inference units and use the model for 8 hours, you are charged for 16 inference hours \(8 hours running time \* two inference units\)\. For more information, see [Amazon Lookout for Vision Pricing](https://aws.amazon.com/lookout-for-vision/pricing/)\. If you don't explicitly stop your model by calling [StopModel](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_StopModel), you are charged even if you are not actively analyzing images with your model\.

The transactions per second \(TPS\) that a single inference unit supports is affected by the following:
+ The algorithm that Lookout for Vision uses to train the model\. When you train a model, multiple models are trained\. Lookout for Vision selects the model with the best performance based on the size of the dataset and its composition of normal and anomalous images\. 
+ Higher resolution images require more time for analysis\.
+ Smaller sized images \(measured in MBs\) are analyzed faster than larger images\.

### Managing throughput with inference units<a name="running-model-manage-throughput"></a>

You can increase or decrease the throughput of your model depending on the demands on your application\. To increase throughput, use additional inference units\. Each additional inference unit increases your processing speed by one inference unit\. For information about calculating the number of inference units that you need, see [Calculate inference units for Amazon Rekognition Custom Labels and Amazon Lookout for Vision models](https://aws.amazon.com/blogs/machine-learning/calculate-inference-units-for-an-amazon-rekognition-custom-labels-model/)\. If you want to change the supported throughput of your model, you have two options: 

#### Manually add or remove inference units<a name="running-model-manual-inference-units"></a>

[Stop](run-stop-model.md) the model and then [restart](run-start-model.md) with the required number of inference units\. The disadvantage with this approach is that the model can't receive requests while it's restarting and can't be used to handle spikes in demand\. Use this approach if your model has steady throughput and your use case can tolerate 10–20 minutes of downtime\. An example would be if you want to batch calls to your model using a weekly schedule\. 

#### Auto\-scale inference units<a name="running-model-auto-scale-inference-units"></a>

If your model has to accommodate spikes in demand, Amazon Lookout for Vision can automatically scale the number of inference units that your model uses\. As demand increases, Amazon Lookout for Vision adds additional inference units to the model and removes them when demand decreases\. 

To let Amazon Lookout for Vision automatically scale inference units for a model, [start](run-start-model.md) the model and specify the `MaxInferenceUnits` parameter\. `MaxInferenceUnits` is the maximum number of inference units that Amazon Lookout for Vision can use to automatically scale the model\. It lets you manage the cost of running the model by limiting the maximum number of inference units available to the model\. If you don't set a maximum number of units, Amazon Lookout for Vision doesn't automatically scale your model\. In that case, the model also only uses the number of inference units that you started with\. 

To find out the maximum number of inference units that you requested for a model, call [DescribeModel](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_DescribeModel) and check the `MaxInferenceUnits` field in the response\. 

## Availability Zones<a name="running-model-availability-zones"></a>

Amazon Lookout for Vision; distributes inference units across multiple Availability Zones within an AWS Region to provide increased availability\. For more information, see [Availability Zones](https://aws.amazon.com/about-aws/global-infrastructure/regions_az/#Availability_Zones)\. To help protect your production models from Availability Zone outages and inference unit failures, start your production models with at least two inference units\. 

If an Availability Zone outage occurs, all inference units in the Availability Zone are unavailable and model capacity is reduced\. Calls to [DetectAnomalies](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_DetectAnomalies) are redistributed across the remaining inference units\. Such calls succeed if they don’t exceed the supported Transactions Per Seconds \(TPS\) of the remaining inference units\. After AWS repairs the Availability Zone, the inference units are restarted, and full capacity is restored\. 

If a single inference unit fails, Amazon Lookout for Vision automatically starts a new inference unit in the same Availability Zone\. Model capacity is reduced until the new inference unit starts\.