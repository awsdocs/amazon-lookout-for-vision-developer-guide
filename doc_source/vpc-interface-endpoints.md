# Access Amazon Lookout for Vision using an interface endpoint \(AWS PrivateLink\)<a name="vpc-interface-endpoints"></a>

You can use AWS PrivateLink to create a private connection between your VPC and Amazon Lookout for Vision\. You can access Lookout for Vision as if it were in your VPC, without the use of an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection\. Instances in your VPC don't need public IP addresses to access Lookout for Vision\.

You establish this private connection by creating an *interface endpoint*, powered by AWS PrivateLink\. We create an endpoint network interface in each subnet that you enable for the interface endpoint\. These are requester\-managed network interfaces that serve as the entry point for traffic destined for Lookout for Vision\.

For more information, see [Access AWS services through AWS PrivateLink](https://docs.aws.amazon.com/vpc/latest/privatelink/privatelink-access-aws-services.html) in the *AWS PrivateLink Guide*\.

## Considerations for Lookout for Vision VPC endpoints<a name="vpc-endpoint-considerations"></a>

Before you set up an interface endpoint for Lookout for Vision, review [Considerations](https://docs.aws.amazon.com/vpc/latest/privatelink/create-interface-endpoint.html#considerations-interface-endpoints) in the *AWS PrivateLink Guide*\.

Lookout for Vision supports making calls to all of its API actions through the interface endpoint\.

VPC endpoint policies are not supported for Lookout for Vision\. By default, full access to Lookout for Vision is allowed through the interface endpoint\. Alternatively, you can associate a security group with the endpoint network interfaces to control traffic to Lookout for Vision through the interface endpoint\.

## Creating an interface VPC endpoint for Lookout for Vision<a name="vpc-endpoint-create"></a>

You can create an interface endpoint for Lookout for Vision using either the Amazon VPC console or the AWS Command Line Interface \(AWS CLI\)\. For more information, see [Create an interface endpoint](https://docs.aws.amazon.com/vpc/latest/privatelink/create-interface-endpoint.html#create-interface-endpoint-aws) in the *AWS PrivateLink Guide*\.

Create an interface endpoint for Lookout for Vision using the following service name:

```
com.amazonaws.region.lookoutvision
```

If you enable private DNS for the interface endpoint, you can make API requests to Lookout for Vision using its default Regional DNS name\. For example, `lookoutvision.us-east-1.amazonaws.com`\.

## Creating a VPC endpoint policy for Lookout for Vision<a name="vpc-endpoint-policy"></a>

An endpoint policy is an IAM resource that you can attach to an interface endpoint\. The default endpoint policy allows full access to Lookout for Vision through the interface endpoint\. To control the access allowed to Lookout for Vision from your VPC, attach a custom endpoint policy to the interface endpoint\.

An endpoint policy specifies the following information:
+ The principals that can perform actions \(AWS accounts, IAM users, and IAM roles\)\.
+ The actions that can be performed\.
+ The resources on which the actions can be performed\.

For more information, see [Control access to services using endpoint policies](https://docs.aws.amazon.com/vpc/latest/privatelink/vpc-endpoints-access.html) in the *AWS PrivateLink Guide*\.

**Example: VPC endpoint policy for Lookout for Vision actions**  
The following is an example of a custom endpoint policy for Lookout for Vision\. When you attach this policy to your interface endpoint, it specifies that all users who have access to the VPC interface endpoint are allowed to call the `DetectAnomalies` API operation for the Lookout for Vision model `myModel` associated with project `myProject`\.

```
{
   "Statement":[
      {
         "Principal":"*",
         "Effect":"Allow",
         "Action":[
            "lookoutvision:DetectAnomalies"
         ],
         "Resource": "arn:aws:lookoutvision:us-west-2:123456789012:model/myProject/myModel"
      }
   ]
}
```