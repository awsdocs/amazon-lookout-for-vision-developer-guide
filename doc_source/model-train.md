--------

Amazon Lookout for Vision is in preview release and is subject to change\.

--------

# Training your model<a name="model-train"></a>

After you have created your datasets and labeled the images, you can train your model\. As part of the training process, a test dataset is used\. If you have a single dataset project, the images in the dataset are automatically split into a test dataset and a training dataset as part of the training process\. If your project has a training and a test dataset, they are used to separately train and test the dataset\. 

After training is complete, you can evaluate the performance of the model and make any necessary improvements\. For more information, see [Improving your model](improve.md)\.

By default your images are encrypted with a key that AWS owns and manages\. You can also choose to use your own AWS Key Management Service \(KMS\) key\. For more information, see [AWS Key Management Service concepts](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#master_keys)\.

You can assign metadata to your model in the form of tags\. For more information, see [Tagging models](tagging-model.md)\.

Each time you train a model, a new version of the model is created\. If you no longer need a version of a model, you can delete it\. For more information, see [Deleting a model](delete-model.md)\. 

To view the existing models in a project, [Viewing your models](view-models.md)\.

**Note**  
If you've just completed [Creating your dataset](model-create-dataset.md) or [Editing your dataset](edit-dataset.md)\. The console should currently show your model dashboard and you don't need to do steps 1 \- 4\.

## Training a model \(console\)<a name="create-model-console"></a>

The following procedure shows you how to train your model using the console\.

**To train your model \(console\)**

1. Open the Amazon Lookout for Vision console at [ https://console\.aws\.amazon\.com/lookoutvision/]( https://console.aws.amazon.com/lookoutvision/)\.

1. In the left navigation pane, choose **Projects**\.

1. In the **Projects** page, choose the project that contains the model that you want to train\. 

1. On the project details page, choose **Train model**\. The **Train model** button is available if you have enough labeled images to train the model\. If the button isn't available, [add more images](edit-dataset.md#add-more-images-dataset) until you have enough labeled images\. 

1. \(Optional\) If you want to use your own AWS KMS encryption key, do the following:

   1. In **Image data encryption** choose **Customize encryption settings \(advanced\)**\.

   1. In **encryption\.aws\_kms\_key** enter the Amazon Resource Name \(ARN\) of your key, or choose an existing AWS KMS key\. To create a new key, choose **Create an AWS IMS key**\.

1. \(Optional\) if you want to add tags to your model do the following:

   1. In the **Tags** section enter the following:

      1. The name of the key in **Key**\.

      1. The value of the key in **Value**\.

   1. To add more tags, choose **Add more tags** and repeat step 6a\.

   1. \(Optional\) If you want to remove a tag, choose **Remove** next to the tag that you want to remove\.

1. Choose **Train model**\. 

1. In the **Do you want to train your model?** dialog box, choose **Train model**\. 

1. In the **Models** view, you can see that training has started and you can check the current status by viewing the `Status` column for the model version\. Training a model takes a while to complete\. 

1. When training is finished, you can evaluate its performance\. For more information, see [Improving your model](improve.md)\.

## Training a model \(SDK\)<a name="create-model-sdk"></a>

You use the [CreateModel](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/CreateModel) operation to start the training, testing and evaluation of a model\. Amazon Lookout for Vision trains the model using the training and test dataset associated with the project\. For more information, see [Creating a project \(SDK\)](model-create-project.md#create-project-sdk)\.

 Each time you call `CreateModel`, a new version of the model is created\. The response from `CreateModel` includes the version of the model\. 

Training takes a while to complete\. To check the current status, call `DescribeModel` and pass the project name \(specified in the call to `CreateProject`\) and the model version\. The `status` field indicates the current status of the model training\. For example code, see [Viewing your models \(SDK\)](view-models.md#view-models-sdk)\. 

If training is successful, you can evaluate model\. For more information, see [Improving your model](improve.md)\. 

To view the models that you have created in a project, call `ListModels`\. For example code, see [Viewing your models](view-models.md)\. 

**To train a model \(SDK\)**

1. If you haven't already done so, do the following:

   1. Create or update an IAM user with `AmazonLookoutVisionFullAccess` and permissions\. For more information, see [Step 2: Create an IAM administrator user and group](su-account-user.md)\.

   1. Install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 4: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

1. Use the following example code to train a model\.

------
#### [ CLI ]

   Change the following values:
   + `project-name` to the name of the project that contains the model that you want to create\.
   + `output-config` to the location where you want to save training results\. Replace the following values:
     + `output bucket` with the name of the Amazon S3 bucket where Amazon Lookout for Vision saves the training results\.
     + `output folder` with the name of the folder where you want to save the training results\. 
     + `Key` with the name of a tag key\. 
     + `Value` with a value to associate with `tag_key`\.

   ```
   aws lookoutvision create-model --project-name "project name"\
     --output-config '{ "S3Location": { "Bucket": "output bucket", "Prefix":  "output folder" } }'\
     --tags '[{"Key":"Key","Value":"Value"}]'
   ```

------
#### [ Python ]

   Change the following values:
   + `project-name` to the name of the project that contains the model that you want to create\.
   + `output-config` to the location where you want to save training results\. Replace the following values:
     + `output_bucket` with the name of the Amazon S3 bucket where Amazon Lookout for Vision saves the training results\.
     + `output_folder` with the name of the folder where you want to save the training results\. 
     + `tag_key` with the name of a tag key\. 
     + `tag_key_value` with a value to associate with `tag_key`\.

   ```
   #Copyright 2021 Amazon.com, Inc. or its affiliates. All Rights Reserved.
   
   import boto3
   import json
   
   def create_model(project_name, output_bucket, output_folder, tag_key, tag_key_value):
   
       client=boto3.client('lookoutvision')
   
       try: 
           #Create a model
           print('Creating model...')
   
           output_config=json.loads('{ "S3Location": { "Bucket": "' + output_bucket + '", "Prefix": "'+ output_folder + '" } } ')
           tags=json.loads('[{"Key": "' + tag_key +'" ,"Value":"' + tag_key_value + '"}]')
           
           response=client.create_model(ProjectName=project_name, OutputConfig=output_config, Tags=tags)
           print('ARN: ' + response['ModelMetadata']['ModelArn'])
           print('Version: ' + response['ModelMetadata']['ModelVersion'])
           print('Status: ' + response['ModelMetadata']['Status'])
           print('Message: ' + response['ModelMetadata']['StatusMessage'])
           print('Done...')
   
       
       except Exception as e:
           print(e)
       
   def main():
       project_name='my-project-python'
       output_bucket = 'output-bucket'
       output_folder = 'my-project-python-output/'
       tag_key = 'my-tag-key'
       tag_key_value = 'my-tag-key-value'
   
       create_model(project_name, output_bucket, output_folder, tag_key, tag_key_value)
   
   if __name__ == "__main__":
       main()
   ```

------