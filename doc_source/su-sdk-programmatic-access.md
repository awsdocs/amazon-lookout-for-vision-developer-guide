# Grant programmatic access<a name="su-sdk-programmatic-access"></a>

You can run the AWS CLI and code examples in this guide on your local computer or other AWS environments, such as an Amazon Elastic Compute Cloud instance\. To run the examples, you need to grant access to the AWS SDK operations that the examples use\. 

**Topics**
+ [Running code on your local computer](#su-sdk-programmatic-access-general)
+ [Running code in AWS environments](#su-sdk-aws-environments)

## Running code on your local computer<a name="su-sdk-programmatic-access-general"></a>

To run code on a local computer, we recommend that you use short\-term credentials to grant a user access to AWS SDK operations\. For specific information about running the AWS CLI and code examples on a local computer, see [Using a profile on your local computer](#su-sdk-programmatic-access-lookoutvision-examples)\.

Users need programmatic access if they want to interact with AWS outside of the AWS Management Console\. The way to grant programmatic access depends on the type of user that's accessing AWS:
+ If you manage identities in IAM Identity Center, the AWS APIs require a profile, and the AWS Command Line Interface requires a profile or an environment variable\.
+ If you have IAM users, the AWS APIs and the AWS Command Line Interface require access keys\. Whenever possible, create temporary credentials that consist of an access key ID, a secret access key, and a security token that indicates when the credentials expire\.

To grant users programmatic access, choose one of the following options\.


****  

| Which user needs programmatic access? | To | By | 
| --- | --- | --- | 
|  Workforce identity \(Users managed in IAM Identity Center\)  | Use short\-term credentials to sign programmatic requests to the AWS CLI or AWS APIs \(directly or by using the AWS SDKs\)\. |  Following the instructions for the interface that you want to use: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/lookout-for-vision/latest/developer-guide/su-sdk-programmatic-access.html)  | 
| IAM | Use short\-term credentials to sign programmatic requests to the AWS CLI or AWS APIs \(directly or by using the AWS SDKs\)\. | Following the instructions in [Using temporary credentials with AWS resources](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_use-resources.html) in the IAM User Guide\. | 
| IAM | Use long\-term credentials to sign programmatic requests to the AWS CLI or AWS APIs \(directly or by using the AWS SDKs\)\.\(Not recommended\) | Following the instructions in [Managing access keys for IAM users](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html) in the IAM User Guide\. | 

### Using a profile on your local computer<a name="su-sdk-programmatic-access-lookoutvision-examples"></a>

You can run the AWS CLI and code examples in this guide with the short\-term credentials you create in [Running code on your local computer](#su-sdk-programmatic-access-general)\. To get the credentials and other settings information, the examples use a profile named `lookoutvision-access` For example: 

```
session = boto3.Session(profile_name='lookoutvision-access')
lookoutvision_client = session.client("lookoutvision")
```

The user that the profile represents must have permissions to call the Lookout for Vision SDK operations and other AWS SDK operations needed by the examples\. For more information, see [Set up SDK permissions](su-sdk-permissions.md)\. To assign permissions, see [Assigning permissions](su-sdk-permissions.md#su-sdk-assign-permissions)\.

To create a profile that works with the AWS CLI and code examples, choose one of the following\. Make sure the name of the profile you create is `lookoutvision-access`\.
+ Users managed by IAM — Follow the instructions at [Switching to an IAM role \(AWS CLI\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-cli.html)\. 
+ Workforce identity \(Users managed by AWS IAM Identity Center \(successor to AWS Single Sign\-On\)\) — Follow the instructions at [Configuring the AWS CLI to use AWS IAM Identity Center \(successor to AWS Single Sign\-On\)](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-sso.html)\. For the code examples, we recommend using an Integrated Development Environment \(IDE\), which supports the AWS Toolkit enabling authentication through IAM Identity Center\. For the Java examples, see [Start building with Java](https://aws.amazon.com/developer/language/java/)\. For the Python examples, see [Start building with Python](https://aws.amazon.com/developer/tools/#IDE_and_IDE_Toolkits)\. For more information, see [IAM Identity Center credentials](https://docs.aws.amazon.com/sdkref/latest/guide/feature-sso-credentials.html)\.

**Note**  
You can use code to get short\-term credentials\. For more information, see [Switching to an IAM role \(AWS API\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-api.html)\. For IAM Identity Center, get the short\-term credentials for a role by following the instructions at [Getting IAM role credentials for CLI access](https://docs.aws.amazon.com/singlesignon/latest/userguide/howtogetcredentials.html)\. 

## Running code in AWS environments<a name="su-sdk-aws-environments"></a>

You shouldn't use user credentials to sign AWS SDK calls in AWS environments, such as production code running in an AWS Lambda function\. Instead, you configure a role that defines the permissions that your code needs\. You then attach the role to the environment that your code runs in\. How you attach the role and make temporary credentials available varies depending on the environment that your code runs in:
+ AWS Lambda function — Use the temporary credentials that Lambda automatically provides to your function when it assumes the Lambda function's execution role\. The credentials are available in the Lambda environment variables\. You don't need to specify a profile\. For more information, see [Lambda execution role](https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html)\.
+ Amazon EC2 — Use the Amazon EC2 instance metadata endpoint credentials provider\. The provider automatically generates and refreshes credentials for you using the Amazon EC2 *instance profile* you attach to the Amazon EC2 instance\. For more information, see [Using an IAM role to grant permissions to applications running on Amazon EC2 instances](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2.html)
+ Amazon Elastic Container Service — Use the Container credentials provider\. Amazon ECS sends and refreshes credentials to a metadata endpoint\. A *task IAM role* that you specify provides a strategy for managing the credentials that your application uses\. For more information, see [Interact with AWS services](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html)\.
+ Greengrass core device — Use X\.509 certificates to connect to AWS IoT Core using TLS mutual authentication protocols\. These certificates let devices interact with AWS IoT without AWS credentials\. The AWS IoT credentials provider authenticates devices using the X\.509 certificate and issues AWS credentials in the form of a temporary, limited\-privilege security token\. For more information, see [Interact with AWS services](https://docs.aws.amazon.com/greengrass/v2/developerguide/interact-with-aws-services.html)\.

For more information about credential providers, see [Standardized credential providers](https://docs.aws.amazon.com/sdkref/latest/guide/standardized-credentials.html)\.