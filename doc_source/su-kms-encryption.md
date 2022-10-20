# Step 6: \(Optional\) Using your own AWS Key Management Service key<a name="su-kms-encryption"></a>

You can use AWS Key Management Service \(KMS\) to manage encryption for the input images that you store in Amazon S3 buckets\.

By default your images are encrypted with a key that AWS owns and manages\. You can also choose to use your own AWS Key Management Service \(KMS\) key\. For more information, see [AWS Key Management Service concepts](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#master_keys)\.

If you want to use your own KMS key, use the the following policy to specify the KMS key\. Change *kms\_key\_arn* to the ARN of the KMS key \(or KMS alias ARN\) that you want to use\. Alternatively, specify `*` to use any KMS key\. For information about adding the policy to an IAM user or role, see [Creating IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html)\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "LookoutVisionKmsDescribeAccess",
            "Effect": "Allow",
            "Action": "kms:DescribeKey",
            "Resource": "kms_key_arn"
        },
        {
            "Sid": "LookoutVisionKmsCreateGrantAccess",
            "Effect": "Allow",
            "Action": "kms:CreateGrant",
            "Resource": "kms_key_arn",
            "Condition": {
                "StringLike": {
                    "kms:ViaService": "lookoutvision.*.amazonaws.com"
                },
                "Bool": {
                    "kms:GrantIsForAWSResource": "true"
                }
            }
        }
    ]
}
```