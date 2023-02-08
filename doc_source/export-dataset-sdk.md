# Exporting datasets from a project \(SDK\)<a name="export-dataset-sdk"></a>

You can use the AWS SDK to export datasets from an Amazon Lookout for Vision project to an Amazon S3 bucket location\. 

By exporting a dataset, you can do tasks such as creating a Lookout for Vision project with a copy of a source project's datasets\. You also can create a snapshot of the datasets used for a specific version of a model\. 

The Python code in this procedure exports the training dataset \(manifest and dataset images\) for a project to a destination Amazon S3 location that you specify\. If present in the project, the code also exports the test dataset manifest and dataset images\. The destination can be in the same Amazon S3 bucket as the source project, or a different Amazon S3 bucket\. The code uses the [ListDatasetEntries](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_ListDatasetEntries.html) operation to get the dataset manifest files\. Amazon S3 operations copy the dataset images and updated manifest files to the destination Amazon S3 location\. 

This procedure shows how to export a project's datasets\. It also shows how to create a new project with the exported datasets\.

**To export the datasets from a project \(SDK\)**

1. If you haven't already done so, install and configure the AWS CLI and the AWS SDKs\. For more information, see [Step 4: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

1. Determine the destination Amazon S3 path for the dataset export\. Be sure that the destination is in an [AWS Region](https://docs.aws.amazon.com/general/latest/gr/lookoutvision.html) that Amazon Lookout for Vision supports\. To create a new Amazon S3 bucket, see [Creating a bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/create-bucket-overview.html)\.

1. Make sure the user has access permissions to the destination Amazon S3 path for the dataset export and the S3 locations for the image files in the source project datasets\. You can use the following policy which assumes the images files can be in any location\. Replace *bucket/path* with the destination bucket and path for the dataset export\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "PutExports",
               "Effect": "Allow",
               "Action": [
                   "S3:PutObjectTagging",
                   "S3:PutObject"
               ],
               "Resource": "arn:aws:s3:::bucket/path/*"
           },
           {
               "Sid": "GetSourceRefs",
               "Effect": "Allow",
               "Action": [
                   "s3:GetObject",
                   "s3:GetObjectTagging",
                   "s3:GetObjectVersion"
               ],
               "Resource": "*"
           }
       ]
   }
   ```

   To provide access, add permissions to your users, groups, or roles:
   + Users and groups in AWS IAM Identity Center \(successor to AWS Single Sign\-On\):

     Create a permission set\. Follow the instructions in [Create a permission set](https://docs.aws.amazon.com/singlesignon/latest/userguide/howtocreatepermissionset.html) in the *AWS IAM Identity Center \(successor to AWS Single Sign\-On\) User Guide*\.
   + Users managed in IAM through an identity provider:

     Create a role for identity federation\. Follow the instructions in [Creating a role for a third\-party identity provider \(federation\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-idp.html) in the *IAM User Guide*\.
   + IAM users:
     + Create a role that your user can assume\. Follow the instructions in [Creating a role for an IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html) in the *IAM User Guide*\.
     + \(Not recommended\) Attach a policy directly to a user or add a user to a user group\. Follow the instructions in [Adding permissions to a user \(console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_change-permissions.html#users_change_permissions-add-console) in the *IAM User Guide*\.

1. Save the following code to a file named `dataset_export.py`\. 

   ```
   """
   Purpose
   
   Shows how to export the datasets (manifest files and images)
   from an Amazon Lookout for Vision project to a new Amazon 
   S3 location.
   """
   
   import argparse
   import json
   import logging
   
   import boto3
   from botocore.exceptions import ClientError
   
   logger = logging.getLogger(__name__)
   
   
   def copy_file(s3_resource, source_file,  destination_file):
       """
       Copies a file from a source Amazon S3 folder to a destination
       Amazon S3 folder.
       The destination can be in a different S3 bucket.
       :param s3: An Amazon S3 Boto3 resource.
       :param source_file: The Amazon S3 path to the source file.
       :param destination_file: The destination Amazon S3 path for 
       the copy operation.
       """
   
       source_bucket, source_key = source_file.replace("s3://", "").split("/", 1)
       destination_bucket, destination_key = destination_file.replace(
           "s3://", "").split("/", 1)
   
       try:
   
           bucket = s3_resource.Bucket(destination_bucket)
           dest_object = bucket.Object(destination_key)
           dest_object.copy_from(CopySource={
               'Bucket': source_bucket,
               'Key': source_key
           })
           dest_object.wait_until_exists()
           logger.info("Copied %s to %s", source_file, destination_file)
       except ClientError as error:
           if error.response['Error']['Code'] == '404':
               error_message = f"Failed to copy {source_file} to " \
                   f"{destination_file}. : {error.response['Error']['Message']}"
               logger.warning(error_message)
               error.response['Error']['Message'] = error_message
           raise
   
   
   def upload_manifest_file(s3_resource, manifest_file, destination):
       """
       Uploads a manifest file to a destination Amazon S3 folder.
       :param s3: An Amazon S3 Boto3 resource.
       :param manifest_file: The manifest file that you want to upload.
       :destination: The Amazon S3 folder location to upload the manifest
       file to.
       """
   
       destination_bucket, destination_key = destination.replace(
           "s3://", "").split("/", 1)
   
       bucket = s3_resource.Bucket(destination_bucket)
   
       put_data = open(manifest_file, 'rb')
       obj = bucket.Object(destination_key + manifest_file)
   
       try:
           obj.put(Body=put_data)
           obj.wait_until_exists()
           logger.info(
               "Put manifest file '%s' to bucket '%s'.", obj.key,
               obj.bucket_name)
       except ClientError:
           logger.exception(
               "Couldn't put manifest file '%s' to bucket '%s'.", obj.key,
               obj.bucket_name)
           raise
       finally:
           if getattr(put_data, 'close', None):
               put_data.close()
   
   
   def get_dataset_types(lookoutvision_client, project):
       """
       Determines the types of the datasets (train or test) in an
       Amazon Lookout for Vision project.
       :param lookoutvision_client: A Lookout for Vision Boto3 client.
       :param project: The Lookout for Vision project that you want to check.
       :return: The dataset types in the project.
       """
   
       try:
           response = lookoutvision_client.describe_project(ProjectName=project)
   
           datasets = []
   
           for dataset in response['ProjectDescription']['Datasets']:
               if dataset['Status'] in ("CREATE_COMPLETE", "UPDATE_COMPLETE"):
                   datasets.append(dataset['DatasetType'])
           return datasets
   
       except lookoutvision_client.exceptions.ResourceNotFoundException:
           logger.exception("Project %s not found.", project)
           raise
   
   
   def process_json_line(s3_resource, entry, dataset_type, destination):
       """
       Creates a JSON line for a new manifest file, copies image and mask to
       destination.
       :param s3_resource: An Amazon S3 Boto3 resource.
       :param entry: A JSON line from the manifest file.
       :param dataset_type: The type (train or test) of the dataset that
       you want to create the manifest file for.
       :param destination: The destination Amazon S3 folder for the manifest
       file and dataset images.
       :return: A JSON line with details for the destination location.
       """
       entry_json = json.loads(entry)
   
       print(f"source: {entry_json['source-ref']}")
   
       # Use existing folder paths to ensure console added image names don't clash.
       bucket, key = entry_json['source-ref'].replace(
           "s3://", "").split("/", 1)
       logger.info("Source location: %s/%s", bucket, key)
   
       destination_image_location = destination + dataset_type + "/images/" + key
   
       copy_file(s3_resource, entry_json['source-ref'],
                 destination_image_location)
   
       # Update JSON for writing.
       entry_json['source-ref'] = destination_image_location
   
       if 'anomaly-mask-ref' in entry_json:
   
           source_anomaly_ref = entry_json['anomaly-mask-ref']
           mask_bucket, mask_key = source_anomaly_ref.replace(
               "s3://", "").split("/", 1)
   
           destination_mask_location = destination + dataset_type + "/masks/" + \
               mask_key
           entry_json['anomaly-mask-ref'] = destination_mask_location
   
           copy_file(s3_resource, source_anomaly_ref,
                     entry_json['anomaly-mask-ref'])
   
       return entry_json
   
   
   def write_manifest_file(lookoutvision_client, s3_resource, project,  dataset_type, destination):
       """
       Creates a manifest file for a dataset. Copies the manifest file and
       dataset images (and masks, if present) to the specified Amazon S3 destination.
       :param lookoutvision_client: A Lookout for Vision Boto3 client.
       :param project: The Lookout for Vision project that you want to use.
       :param dataset_type: The type (train or test) of the dataset that
       you want to create the manifest file for.
       :param destination: The destination Amazon S3 folder for the manifest file
       and dataset images.
       """
   
       try:
   
           # Create a reusable Paginator
           paginator = lookoutvision_client.get_paginator('list_dataset_entries')
   
           # Create a PageIterator from the Paginator
           page_iterator = paginator.paginate(ProjectName=project,
                                              DatasetType=dataset_type,
                                              PaginationConfig={
                                                  'PageSize': 100
                                              }
                                              )
   
           output_manifest_file = dataset_type + ".manifest"
   
           # Create manifest file then upload to Amazon S3 with images.
           with open(output_manifest_file, "w", encoding="utf-8") as manifest_file:
   
               for page in page_iterator:
                   for entry in page['DatasetEntries']:
   
                       try:
                           entry_json = process_json_line(
                               s3_resource, entry, dataset_type, destination)
   
                           manifest_file.write(json.dumps(entry_json) + "\n")
   
                       except ClientError as error:
                           if error.response['Error']['Code'] == '404':
                               print(error.response['Error']['Message'])
                               print(f"Excluded JSON line: {entry}")
                           else:
                               raise
           upload_manifest_file(s3_resource, output_manifest_file,
                                destination + "datasets/")
   
       except ClientError:
           logger.exception("Problem getting dataset_entries")
           raise
   
   
   def export_datasets(lookoutvision_client, s3_resource, project, destination):
       """
       Exports the datasets from an Amazon Lookout for Vision project to a specified
       Amazon S3 destination.
       :param project: The Lookout for Vision project that you want to use.
       :param destination: The destination Amazon S3 folder for the exported datasets.
       """
       # Add trailing backslash, if missing.
       destination = destination if destination[-1] == "/"  \
           else destination+"/"
   
       print(f"Exporting project {project} datasets to {destination}.")
   
       # Get each dataset and export to destination.
   
       dataset_types = get_dataset_types(lookoutvision_client, project)
       for dataset in dataset_types:
   
           logger.info("Copying %s dataset to %s.", dataset, destination)
   
           write_manifest_file(lookoutvision_client, s3_resource, project, dataset,
                               destination)
   
       print("Exported dataset locations")
       for dataset in dataset_types:
           print(f"   {dataset}: {destination}datasets/{dataset}.manifest")
   
       print("Done.")
   
   
   def add_arguments(parser):
       """
       Adds command line arguments to the parser.
       :param parser: The command line parser.
       """
   
       parser.add_argument(
           "project", help="The project that contains the dataset.")
       parser.add_argument("destination", help="The destination Amazon S3 folder.")
   
   
   def main():
       """
       Exports the datasets from an Amazon Lookout for Vision project to a
       destination Amazon S3 location.
       """
       logging.basicConfig(level=logging.INFO,
                           format="%(levelname)s: %(message)s")
       parser = argparse.ArgumentParser(usage=argparse.SUPPRESS)
       add_arguments(parser)
   
       args = parser.parse_args()
   
       try:
   
           lookoutvision_client = boto3.client("lookoutvision")
           s3_resource = boto3.resource('s3')
           export_datasets(lookoutvision_client, s3_resource,
                           args.project, args.destination)
       except ClientError as err:
           logger.exception(err)
           print(f"Failed: {format(err)}")
   
   
   if __name__ == "__main__":
       main()
   ```

1. Run the code\. Supply the following command line arguments:
   + project – The name of the source project that contains the datasets that you want to export\.
   + destination – The destination Amazon S3 path for the datasets\.

   For example, `python dataset_export.py myproject s3://bucket/path/`

1. Note the manifest file locations that the code displays\. You need them in step 8\. 

1. Create a new Lookout for Vision project with exported dataset by following the instructions at [Creating your project](model-create-project.md)\.

1. Do one of the following:
   + Use the Lookout for Vision console to create datasets for your new project by following the instructions at [Creating a dataset with a manifest file \(console\)](create-dataset-use-manifest.md)\. You don't need to do steps 1–6\.

     For step 12, do the following:

     1. If the source project has a test dataset, choose **Separate training and test datasests**, otherwise choose **single dataset**\.

     1. For **\.manifest file location**, enter the location of the appropriate manifest file \(train or test\) that you noted in step 6\.
   + Use the [CreateDataset](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_CreateDataset.html) operation to create datasets for your new project by using the code at [Creating a dataset with a manifest file \(SDK\)](create-dataset-sdk.md)\. For the `manifest_file` parameter, use the manifest file location you noted in step 6\. If the source project has a test dataset, use the code again to create the test dataset\.

1. If you're ready, train the model by following the instructions at [Training your model](model-train.md)\.