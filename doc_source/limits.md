# Quotas in Amazon Lookout for Vision<a name="limits"></a>

The following tables describe the current quotas within Amazon Lookout for Vision\. For information about quotas that can be changed, see [AWS service quotas](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html)\.

## Model quotas<a name="quota-training"></a>

The following quotas apply to the testing, training, and functionality of a model\.


| Resource | Quota | 
| --- | --- | 
| Supported file format | PNG and JPEG image formats | 
| Minimum image dimension of image file in an Amazon S3 bucket | 64 pixels x 64 pixels | 
| Maximum image dimension of image file in an Amazon S3 bucket | 4096 pixels X 4096 pixels is the maximum\. Smaller dimensions are able to upload faster\. | 
| Differing image dimensions of image files used in a project | All images in the dataset must have the same dimensions | 
| Maximum file size for an image in an Amazon S3 bucket | 8 MB | 
| Lack of labels | Images must be labeled as normal or anomaly before training\. Images without labels are ignored during training\. | 
| Minimum number of images labeled normal in training dataset | 10 for a project with separate training and test datasets\. 20 for project with a single dataset\.  | 
| Minimum number of images labeled anomaly in training dataset | 0 for a project with separate training and test datasets\. 10 for a project with a single dataset\.  | 
| Maximum number of images in training dataset | 16,000 | 
| Minimum number of images labeled normal in test dataset | 10 | 
| Minimum number of images labeled anomaly in test dataset | 10 | 
| Maximum number of images in test dataset | 4,000 | 
| Maximum number of images in trial detection dataset | 2,000 | 
| Maximum dataset manifest file size | 1 GB | 
| Maximum number of training datasets in a model | 1 | 
| Maximum training time | 24 hours | 
| Maximum testing time | 24 hours | 
| Maximum number of anomaly labels in a project | 100 | 
| Maximum number of anomaly labels on a mask image | 20 | 
| Minimum number of images for an anomaly label\. To count, the image must contain only one type of anomaly label\.  | 20 for a single dataset project\. 10 for each dataset in a project with separate training and test datasets\. | 
| Maximum number of training images for anomaly localization | 8000 | 
| Maximum number of test images for anomaly localization | 800 | 