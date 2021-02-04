--------

Amazon Lookout for Vision is in preview release and is subject to change\.

--------

# Creating your dataset<a name="model-create-dataset"></a>

A dataset contain the images and assigned labels that you use to train and test a model\. An Amazon Lookout for Vision project can have one of the following dataset configurations:
+ **Single dataset** – A single dataset project uses a single dataset to train and test your model\. Using a single dataset simplifies training by letting Amazon Lookout for Vision choose the training and test images\. During training, Amazon Lookout for Vision, internally splits the dataset into a training dataset and a test dataset\. You don't have access to the split datasets\. We recommend using a single dataset project for most scenarios\.
+ **Separate training and test datasets** – If you want finer control over training, testing, and performance tuning, you can configure your project to have separate training and test datasets\. Use a separate test dataset if you want control over the images used for testing, or if you already have a benchmark set of images that you want to use\.

You can add a test dataset to an existing single dataset project\. The single dataset then becomes the training dataset\. If you remove the test dataset from a project with separate training and test datasets, the project becomes a single dataset project\. For more information, see [Deleting a dataset](delete-dataset.md)\. 

To view the existing models in a project, see [Viewing your datasets](view-datasets.md)\.

For information about dataset image requirements, see [Preparing images for a dataset](model-prepare-images.md)\.

You can create your project's datasets by using images in an Amazon S3 bucket, your local computer, or from an Amazon SageMaker Ground Truth format manifest file\. 

**Topics**
+ [Creating a dataset using images stored on your local computer](create-dataset-computer-upload.md)
+ [Creating a dataset using images stored in an Amazon S3 bucket](create-dataset-s3.md)
+ [Creating a dataset using an Amazon SageMaker Ground Truth manifest file](create-dataset-ground-truth.md)