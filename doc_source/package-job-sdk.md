# Packaging your model \(SDK\)<a name="package-job-sdk"></a>

You package a model as a model component by creating a model packaging job\. To create a model packaging job you call the [StartModelPackagingJob](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_StartModelPackagingJob) API\. The job might take a while to complete\. To find out the current status, call [DescribeModelPackagingJob](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_DescribeModelPackagingJob) and check the `Status` field in the response\. 

For information about package settings, see [Package settings](package-settings.md)\.

The following procedure shows you how to start a packaging job by using the AWS CLI\. You can package the model for a target platform or a target device\.

**To package your model \(SDK\)**

1. If you haven't already done so, do the following:

   1. Create or update an IAM user with permissions to access Amazon Lookout for Vision\. For more information, see [Step 3: Set up permissions](su-setup-permissions.md)\. 

   1. Install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 5: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

1. Make sure the IAM user has the following permissions:
   + `lookoutvision:StartModelPackagingJob`
   + `s3:PutObject`
   + `s3:GetBucketLocation`
   + `greengrass:CreateComponentVersion`
   + `greengrass:DescribeComponent`
   + \(Optional\) `greengrass:TagResource`\. Only required if you want to tag the component\.

1. Use the following CLI commands to package your model for either a target device or a target platform\. 

------
#### [ Target platform ]

   Use the following CLI commands to package a model for a target platform\.

   Change the following values:
   + `project_name` to the name of the project that contains the model that you want to package\.
   + `model_version` to the version of the model that you want to package\.
   + \(Optional\) `description` to a description for your model packaging job\.
   + `architecture` to the architecture \(`ARM64` or `X86_64`\) of the AWS IoT Greengrass Version 2 core device where you run the model component\.
   + `gpu_code` to the gpu code of the core device where you run the model component\.
   + `trt_ver` to the TensorRT version you have installed on your core device\.
   + `cuda_ver` to the CUDA version you have installed on your core device\.
   + `component_name` to a name for the model component that you want to create on AWS IoT Greengrass V2\.
   + \(Optional\) `component_version` to a version for the model component that the packaging job creates\. Use the format `major.minor.patch`\. For example, 1\.0\.0 represents the first major release for a component\. 
   + `bucket` to the Amazon S3 bucket where the packaging job stores the model component artifacts\.
   + `prefix` to the location within the Amazon S3 bucket where the packaging job stores the model component artifacts\.
   + \(Optional\) `component_description` to a description for the model component\.
   + \(Optional\) `tag_key1` and `tag_key2` to the keys for tags that are attached to the model component\.
   + \(Optional\) `tag_value1` and `tag_value2` to the key values for the tags that are attached to the model component\.

   ```
   aws lookoutvision start-model-packaging-job \
     --project-name project_name \
     --model-version model_version \
     --description="description" \
     --configuration "Greengrass={TargetPlatform={Os='LINUX',Arch='architecture',Accelerator='NVIDIA'},CompilerOptions='{\'gpu-code\': \'gpu_code\', \'trt-ver\': \'trt_ver\', \'cuda-ver\': \'cuda_ver\'}',S3OutputLocation={Bucket='bucket',Prefix='prefix'},ComponentName='Component_name',ComponentVersion='component_version',ComponentDescription='component_description',Tags=[{Key='tag_key1',Value='tag_value1'}, {Key='tag_key2',Value='tag_value2'}]}"
   ```

   For example:

   ```
   aws lookoutvision start-model-packaging-job \
     --project-name test-project-01 \
     --model-version 1 \
     --description="Model Packaging Job for G4 Instance using TargetPlatform Option" \
     --configuration "Greengrass={TargetPlatform={Os='LINUX',Arch='X86_64',Accelerator='NVIDIA'},CompilerOptions='{\'gpu-code\': \'sm_75\', \'trt-ver\': \'7.1.3\', \'cuda-ver\': \'10.2\'}',S3OutputLocation={Bucket='bucket',Prefix='test-project-01/folder'},ComponentName='SampleComponentNameX86TargetPlatform',ComponentVersion='0.1.0',ComponentDescription='This is my component',Tags=[{Key='modelKey0',Value='modelValue'}, {Key='modelKey1',Value='modelValue'}]}"
   ```

------
#### [ Target Device ]

   Use the following CLI commands to package a model for a target device\.

   Change the following values:
   + `project_name` to the name of the project that contains the model that you want to package\.
   + `model_version` to the version of the model that you want to package\.
   + \(Optional\) `description` to a description for your model packaging job\.
   + `component_name` to a name for the model component that you want to create on AWS IoT Greengrass V2\.
   + \(Optional\) `component_version` to a version for the model component that the packaging job creates\. Use the format `major.minor.patch`\. For example, 1\.0\.0 represents the first major release for a component\. 
   + `bucket` to the Amazon S3 bucket where the packaging job stores the model component artifacts\.
   + `prefix` to the location within the Amazon S3 bucket where the packaging job stores the model component artifacts\.
   + \(Optional\) `component_description` to a description for the model component\.
   + \(Optional\) `tag_key1` and `tag_key2` to the keys for tags that are attached to the model component\.
   + \(Optional\) `tag_value1` and `tag_value2` to the key values for the tags that are attached to the model component\.

   ```
   aws lookoutvision start-model-packaging-job \
     --project-name project_name \
     --model-version model_version \
     --description="description" \
     --configuration "Greengrass={TargetDevice='jetson_xavier',S3OutputLocation={Bucket='bucket',Prefix='prefix'},ComponentName='component_name',ComponentVersion='component_version',ComponentDescription='component_description',Tags=[{Key='tag_key1',Value='tag_value1'}, {Key='tag_key2',Value='tag_value2'}]}"
   ```

   For example:

   ```
   aws lookoutvision start-model-packaging-job \
     --project-name project_01 \
     --model-version 1 \
     --description="description" \
     --configuration "Greengrass={TargetDevice='jetson_xavier',S3OutputLocation={Bucket='bucket',Prefix='component_folder'},ComponentName='jetson_component',ComponentVersion='2.0.0',ComponentDescription='jetson model component',Tags=[{Key='tag_key1',Value='tag_value1'}, {Key='tag_key2',Value='tag_value2'}]}"
   ```

------

1. Note the value of JobName in the response\. You need it in the next step\. For example: 

   ```
   {
       "JobName": "6bcfd0ff-90c3-4463-9a89-6b4be3daf972"
   }
   ```

1. Use `DescribeModelPackagingJob` to get the current status of the job\. Change the following:
   + `project_name` to the name of the project that you are using\.
   + `job_name` to the name of the job that you noted in the previous step\.

   ```
   aws lookoutvision describe-model-packaging-job \
       --project-name project_name \
       --job-name job_name
   ```

   The model packaging job is complete if the value of `Status` is `SUCCEEDED`\. If the value is different, wait a minute and try again\. 

1. Continue deployment using AWS IoT Greengrass V2\. For more information, see [Deploying your components to a device](device-deploy-components.md)\. 