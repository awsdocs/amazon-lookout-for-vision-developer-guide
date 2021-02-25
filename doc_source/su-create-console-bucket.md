# Step 3: Create the console bucket<a name="su-create-console-bucket"></a>

You use an Amazon Lookout for Vision project to create and manage your models\. When you first open the Amazon Lookout for Vision console in a new AWS Region, Lookout for Vision creates an Amazon S3 bucket \(console bucket\) to store your projects\. You should note the console bucket name somewhere where you can refer to it later because you might need to use the bucket name in AWS SDK operations or console tasks, such as creating a dataset\.

The format of the bucket name is `lookoutvision`\-*<region>*\-*<random value>*\. The random value ensures that there isn't a collision between bucket names\.

**To create the console bucket**

1. Open the Amazon Lookout for Vision console at [ https://console\.aws\.amazon\.com/lookoutvision/]( https://console.aws.amazon.com/lookoutvision/)\.

1. Choose **Get started**\.

1. If this is the first time that you've opened the console in the current AWS Region, do the following in the **First Time Set Up** dialog box:

   1. Copy down the name of the Amazon S3 bucket that's shown\. You'll need this information later\.

   1. Choose **Create S3 bucket** to let Amazon Lookout for Vision create an Amazon S3 bucket \(console bucket\) on your behalf\.

1. Close the browser window\.