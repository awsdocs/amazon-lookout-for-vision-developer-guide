# Create, train, and start a Lookout for Vision model using an AWS SDK<a name="example_lookoutvision_Scenario_CreateTrainStartModel_section"></a>

The following code example shows how to create, train, and start a Lookout for Vision model\.

------
#### [ Python ]

**SDK for Python \(Boto3\)**  
 Creates and optionally starts an Amazon Lookout for Vision model using command line arguments\. The example code creates a new project, training dataset, optional test dataset, and model\. After model training is completed, you can use a provided script to try your model with an image\.   
 This example requires a set of images to train its model\. You can find example circuit board images on GitHub that you can use for training and testing\. For details on how to copy these images to an Amazon Simple Storage Service \(Amazon S3\) bucket, see [Prepare example images](https://docs.aws.amazon.com/lookout-for-vision/latest/developer-guide/su-prepare-example-images.html)\.   
 For complete source code and instructions on how to set up and run, see the full example on [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/python/example_code/lookoutvision#code-examples)\.   

**Services used in this example**
+ Lookout for Vision

------

For a complete list of AWS SDK developer guides and code examples, including help getting started and information about previous versions, see [Using Lookout for Vision with an AWS SDK](getting-started-sdk.md#sdk-general-information-section)\.