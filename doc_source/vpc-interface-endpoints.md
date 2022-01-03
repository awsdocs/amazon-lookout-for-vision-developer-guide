# Amazon Lookout for Vision and interface VPC endpoints \(AWS PrivateLink\)<a name="vpc-interface-endpoints"></a>

You can establish a private connection between your VPC and Amazon Lookout for Vision by creating an *interface VPC endpoint*\. Interface endpoints are powered by [AWS PrivateLink](http://aws.amazon.com/privatelink), a technology that enables you to privately access Lookout for Vision APIs without an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection\. Instances in your VPC don't need public IP addresses to communicate with Lookout for Vision APIs\. Traffic between your VPC and Lookout for Vision does not leave the Amazon network\. 

Each interface endpoint is represented by one or more [Elastic Network Interfaces](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html) in your subnets\. 

For more information, see [Interface VPC endpoints \(AWS PrivateLink\)](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html) in the *Amazon VPC User Guide*\. 

## Considerations for Lookout for Vision VPC endpoints<a name="vpc-endpoint-considerations"></a>

Before you set up an interface VPC endpoint for Lookout for Vision, ensure that you review [Interface endpoint properties and limitations](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#vpce-interface-limitations) in the *Amazon VPC User Guide*\. 

Lookout for Vision supports making calls to all of its API actions from your VPC\. 

VPC endpoint policies are supported for Lookout for Vision\. By default, full access to Lookout for Vision is allowed through the endpoint\. For more information, see [Controlling access to services with VPC endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints-access.html) in the *Amazon VPC User Guide*\.

## Creating an interface VPC endpoint for Lookout for Vision<a name="vpc-endpoint-create"></a>

You can create a VPC endpoint for the Lookout for Vision service using either the Amazon VPC console or the AWS Command Line Interface \(AWS CLI\)\. For more information, see [Creating an interface endpoint](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#create-interface-endpoint) in the *Amazon VPC User Guide*\.

Create a VPC endpoint for Lookout for Vision using the following service name: 
+ com\.amazonaws\.*region*\.lookoutvision 

If you enable private DNS for the endpoint, you can make API requests to Lookout for Vision using its default DNS name for the Region, for example, `lookoutvision.us-east-1.amazonaws.com`\. 

For more information, see [Accessing a service through an interface endpoint](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html#access-service-though-endpoint) in the *Amazon VPC User Guide*\.

## Creating a VPC endpoint policy for Lookout for Vision<a name="vpc-endpoint-policy"></a>

You can attach an endpoint policy to your VPC endpoint that controls access to Lookout for Vision\. The policy specifies the following information:
+ The principal that can perform actions\.
+ The actions that can be performed\.
+ The resources on which actions can be performed\.

For more information, see [Controlling access to services with VPC endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints-access.html) in the *Amazon VPC User Guide*\. 

**Example: VPC endpoint policy for Lookout for Vision actions**  
The following is an example of an endpoint policy for Lookout for Vision\. When attached to an endpoint, this policy specifies that all users who have access to the VPC interface endpoint are allowed to call the `DetectAnomalies` API operation for the Lookout for Vision model `myModel` associated with project `myProject`\.

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