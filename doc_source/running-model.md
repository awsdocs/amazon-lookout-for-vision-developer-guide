--------

Amazon Lookout for Vision is in preview release and is subject to change\.

--------

# Running your trained Amazon Lookout for Vision model<a name="running-model"></a>

To detect anomalies in images with your model, you must first start your model with the `StartModel` operation\.

After your model starts, you can use the `DetectAnomalies` operation to detect anomalies in an image\. For more information, see [Detecting anomalies in an image](inference-detect-anomalies.md)\.  

You are charged for the amount of time, in minutes, that a model runs and for the number of inference units that your model uses\. If you are not using a model, use the `StopModel` operation to stop your model\.

The Amazon Lookout for Vision console provides AWS CLI commands that you can use to start and stop your model\. This section includes example Python code that you can use\. 



**Topics**
+ [Starting your Amazon Lookout for Vision model](run-start-model.md)
+ [Stopping your Amazon Lookout for Vision model](run-stop-model.md)