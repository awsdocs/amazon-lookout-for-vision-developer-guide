# Installing the AWS SDKS<a name="sdk-install-sdk"></a>

Follow the steps to download and configure the AWS SDKs\.

**To set up the AWS CLI and the AWS SDKs**

1. Download and install the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) and the AWS SDKs that you want to use\. This guide provides examples for the AWS CLI, [Java](https://docs.aws.amazon.com/sdk-for-java/latest/developer-guide/setup.html), and [Python](https://boto3.amazonaws.com/v1/documentation/api/latest/guide/quickstart.html#installation)\. For information about installing AWS SDKs, see [Tools for Amazon Web Services](https://aws.amazon.com/tools/)\.

1. Create an access key for the user you created in [Step 2: Create an IAM administrator user and group](su-account-user.md)\.

   1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

   1. In the navigation pane, choose **Users**\.

   1. Choose the name of the user you created in [Step 2: Create an IAM administrator user and group](su-account-user.md)\.

   1. Choose the **Security credentials** tab\.

   1. Choose **Create access key**, choose **Download \.csv file** to save the access key ID and secret access key to a CSV file on your computer, and then choose **Close**\. 
**Important**  
Store the file in a secure location\. You will not have access to the secret access key again after this dialog box closes\.

1. If you have installed the AWS CLI, you can configure the credentials and Region for most AWS SDKs by entering `aws configure` at the command prompt\. Otherwise, use the following instructions\.

   1. On your computer, navigate to your home directory, and create an `.aws` directory\. On Unix\-based systems, such as Linux or macOS, this is in the following location: 

      ```
      ~/.aws
      ```

      On Windows, this is in the following location:

      ```
      %HOMEPATH%\.aws
      ```

   1. In the `.aws` directory, create a new file named `credentials`\. 

   1. Open the credentials CSV file that you created in step 2\. Copy its contents into the `credentials` file using the following format:

      ```
      [default]
      aws_access_key_id = your_access_key_id
      aws_secret_access_key = your_secret_access_key
      ```

      Substitute your access key ID and secret access key for *your\_access\_key\_id* and *your\_secret\_access\_key*\.

   1. Save the `Credentials` file and delete the CSV file\.

   1. In the `.aws` directory, create a new file named `config`\. 

   1. Open the `config` file and enter your Region in the following format\.

      ```
      [default]
      region = your_aws_region
      ```

      Substitute your Region \(for example, `us-west-2`\) for *your\_aws\_region*\. 
**Note**  
If you don't select a Region, us\-east\-1 is used by default\. 

   1. Save the `config` file\.