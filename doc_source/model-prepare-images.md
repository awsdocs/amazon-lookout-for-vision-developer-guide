# Preparing images for a dataset<a name="model-prepare-images"></a>

You need a collection of images to create a dataset\. Your images must be PNG or JPEG format files\.

If you are using a single dataset, you need at least 20 images of normal objects and at least 10 images of anomalous objects\. 

If you are using separate training and test datasets, your training dataset needs a minimum of 10 images of normal objects\. Your test dataset needs at least 10 images of normal objects and at least 10 images of anomalous objects\. To create a higher quality model, use more than the minimum number of images\. 

Your images should be of a single type of object\. Also, you should have consistent image capture conditions, such as camera positioning, lighting, and object pose\. 

All images in a project must have the same dimensions\.

All training and test images must be unique images, preferably of unique objects\. Normal images should capture the normal variations of the object being analyzed\. Anomalous images should capture a diverse sampling of anomalies\.

Amazon Lookout for Vision provides example images that you can use\. For more information, see [Step 8: \(Optional\) Prepare example images](su-prepare-example-images.md)\.

For image limits, see [Quotas in Amazon Lookout for Vision](limits.md)\.