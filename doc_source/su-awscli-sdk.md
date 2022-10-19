# Step 5: Set up the AWS CLI and AWS SDKs<a name="su-awscli-sdk"></a>

The following steps show you how to install the AWS Command Line Interface \(AWS CLI\) and AWS SDKs\. The examples in this documentation use the CLI and SDKs\. There are a number of different ways to authenticate AWS SDK calls\. The examples in this guide assume that you're using a default credentials profile for calling AWS CLI commands and AWS SDK API operations\. To call AWS SDK API operations, the IAM user needs access keys to sign API requests\.

**Topics**
+ [Using this service with an AWS SDK](sdk-general-information-section.md)
+ [Create access keys for your IAM user](#su-access-keys)
+ [Install the AWS SDKS](#w221aac10c19c11)

## Create access keys for your IAM user<a name="su-access-keys"></a>

Access keys consist of an access key ID and secret access key, which are used to sign programmatic requests that you make to AWS\. If you don't have access keys, you can create them from the AWS Management Console\. As a best practice, do not use the AWS account root user access keys for any task where it's not required\. Instead, [create a new administrator IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html) with access keys for yourself\.

The only time that you can view or download the secret access key is when you create the keys\. You cannot recover them later\. However, you can create new access keys at any time\. You must also have permissions to perform the required IAM actions\. For more information, see [Permissions required to access IAM resources](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_permissions-required.html) in the *IAM User Guide*\.

**To create access keys for an IAM user**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Users**\.

1. Choose the name of the user whose access keys you want to create, and then choose the **Security credentials** tab\.

1. In the **Access keys** section, choose **Create access key**\.

1. To view the new access key pair, choose **Show**\. You will not have access to the secret access key again after this dialog box closes\. Your credentials will look something like this:
   + Access key ID: AKIAIOSFODNN7EXAMPLE
   + Secret access key: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY

1. To download the key pair, choose **Download \.csv file**\. Store the keys in a secure location\. You will not have access to the secret access key again after this dialog box closes\.

   Keep the keys confidential in order to protect your AWS account and never email them\. Do not share them outside your organization, even if an inquiry appears to come from AWS or Amazon\.com\. No one who legitimately represents Amazon will ever ask you for your secret key\.

1. After you download the `.csv` file, choose **Close**\. When you create an access key, the key pair is active by default, and you can use the pair right away\.

**Related topics**
+ [What is IAM?](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) in the *IAM User Guide*
+ [AWS security credentials](https://docs.aws.amazon.com/general/latest/gr/aws-security-credentials.html) in *AWS General Reference* 

## Install the AWS SDKS<a name="w221aac10c19c11"></a>

Follow the steps to download and configure the AWS SDKs\.

**To set up the AWS CLI and the AWS SDKs**

1. Download and install the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) and the AWS SDKs that you want to use\. This guide provides examples for the AWS CLI, Java, and Python\. For information about installing AWS SDKs, see [Tools for Amazon Web Services](https://aws.amazon.com/tools/)\.

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

      Substitute your access key ID and secret access key for the *your\_access\_key\_id* and *your\_secret\_access\_key* that you got in [Create access keys for your IAM user](#su-access-keys)\.

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

**Next:** [Step 6: Set up SDK permissions](su-sdk-permissions.md)