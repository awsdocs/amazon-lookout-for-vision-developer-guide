# Step 2: Create an IAM administrator user and group<a name="su-account-user"></a>

If your account already includes an IAM user with full AWS administrative permissions, you can skip this step\.

When you first create an AWS account, you begin with one sign\-in identity that has complete access to all AWS services and resources in the account\. This identity is called the AWS account *root user* and is accessed by signing in with the email address and password that you used to create the account\.

**Important**  
We strongly recommend that you do not use the root user for your everyday tasks\. Safeguard your root user credentials and use them to perform the tasks that only the root user can perform\. For the complete list of tasks that require you to sign in as the root user, see [Tasks that require root user credentials](https://docs.aws.amazon.com/general/latest/gr/root-vs-iam.html#aws_tasks-that-require-root) in the *AWS General Reference*\.

Instead of using the root user for everday tasks, we recommend that you adhere to the best practice in [Create Individual IAM Users](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#create-iam-users), which is to create an AWS Identity and Access Management \(IAM\) administrator user\. Then securely lock away the root user credentials, and use them to perform only a few account and service management tasks\. 

For more information, see [How Amazon Lookout for Vision works with IAM](security_iam_service-with-iam.md)\.

## Create an IAM administrator user and add the user to an administrators group<a name="su-iam-admin-group-user"></a>

To create an administrator user for yourself and add the user to an administrators group, do the following\.

To create an administrator user, choose one of the following options\.


****  

| Choose one way to manage your administrator | To | By | You can also | 
| --- | --- | --- | --- | 
| In IAM Identity Center \(Recommended\) | Use short\-term credentials to access AWS\.This aligns with the security best practices\. For information about best practices, see [Security best practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#bp-users-federation-idp) in the *IAM User Guide*\. | Following the instructions in [Getting started](https://docs.aws.amazon.com/singlesignon/latest/userguide/getting-started.html) in the AWS IAM Identity Center \(successor to AWS Single Sign\-On\) User Guide\. | Configure programmatic access by [Configuring the AWS CLI to use AWS IAM Identity Center \(successor to AWS Single Sign\-On\)](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-sso.html) in the AWS Command Line Interface User Guide\. | 
| In IAM \(Not recommended\) | Use long\-term credentials to access AWS\. | Following the instructions in [Creating your first IAM admin user and user group](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html) in the IAM User Guide\. | Configure programmatic access by [Managing access keys for IAM users](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html) in the IAM User Guide\. | 

## Sign into the AWS console<a name="su-iam-user-signin"></a>

To sign in the AWS console with the IAM user that you just created, do the following\.

**To sign into the AWS console**
+  Sign in to the [IAM console](https://console.aws.amazon.com/iam) by choosing **IAM user** and entering your AWS account ID or account alias\. On the next page, enter your IAM user name and your password\.
**Note**  
For your convenience, the AWS sign\-in page uses a browser cookie to remember your IAM user name and account information\. If you previously signed in as a different user, choose the sign\-in link beneath the button to return to the main sign\-in page\. From there, you can enter your AWS account ID or account alias to be redirected to the IAM user sign\-in page for your account\.

**Next:** [Step 3: Set up permissions](su-setup-permissions.md)