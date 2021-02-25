# Tagging models<a name="tagging-model"></a>

You can identify, organize, search for, and filter your Amazon Lookout for Vision models by using tags\. Each tag is a label consisting of a user\-defined key and value\. For example, to help determine billing for your models, you could tag your models with a `Cost center` key and add the appropriate cost center number as a value\. For more information, see [Tagging AWS resources](https://docs.aws.amazon.com/general/latest/gr/aws_tagging.html)\.

Use tags to:
+ Track billing for a model by using cost allocation tags\. For more information, see [Using Cost Allocation Tags](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.html)\.
+ Control access to a model by using Identity and Access Management \(IAM\)\. For more information, see [Controlling access to AWS resources using resource tags](@url-iam-user;access_tags.html)\.
+ Automate model management\. For example, you can run automated start or stop scripts that turn off development models during non\-business hours to reduce costs\. For more information, see [Running your trained Amazon Lookout for Vision model](running-model.md)\. 

You can tag models by using the Amazon Lookout for Vision console or by using the AWS SDKs\. 

**Topics**
+ [Tagging models \(console\)](#tagging-model-console)
+ [Tagging models \(SDK\)](#tagging-model-sdk)

## Tagging models \(console\)<a name="tagging-model-console"></a>

You can use the Amazon Lookout for Vision console to add tags to models, view the tags attached to a model, and remove tags\. 

### Adding or removing tags \(console\)<a name="tagging-model-add-remove-console"></a>

This procedure explains how to add tags to, or remove tags from, an existing model\. You can also add tags to a new model when it is trained\. For more information, see [Training your model](model-train.md)\. 

**To add tags to, or remove tags from, an existing model \(console\)**

1. Open the Amazon Lookout for Vision console at [ https://console\.aws\.amazon\.com/lookoutvision/]( https://console.aws.amazon.com/lookoutvision/)\.

1. Choose **Get started**\. 

1. In the navigation pane, choose **Projects**\.

1. On the **Projects** resources page, choose the project that contains the model that you want to tag\.

1. In the navigation pane, under the project you previously chose, choose **Models**\.

1. In the **Models** section, choose the model that you want to add a tag to\. 

1. On the model's details page, choose the **Tags** tab\. 

1. In the **Tags** section, choose **Manage tags**\.

1. On the **Manage tags** page, choose **Add new tag**\.

1. Enter a key and a value\.

   1. For **Key**, enter a name for the key\.

   1. For **Value**, enter a value\.

1. To add more tags, repeat steps 9 and 10\.

1. \(Optional\) To remove a tag, choose **Remove** next to the tag that you want to remove\. If you are removing a previously saved tag, it is removed when you save your changes\.

1. Choose **Save changes** to save your changes\.

### Viewing model tags \(console\)<a name="tagging-model-viewing-console"></a>

You can use the Amazon Lookout for Vision console to view the tags attached to a model\.

To view the tags attached to *all models within a project*, you must use the AWS SDK\. For more information, see [Listing model tags \(SDK\)](#listing-model-tags-sdk)\.

**To view the tags attached to a model**

1. Open the Amazon Lookout for Vision console at [ https://console\.aws\.amazon\.com/lookoutvision/]( https://console.aws.amazon.com/lookoutvision/)\.

1. Choose **Get started**\. 

1. In the navigation pane, choose **Projects**\.

1. On the **Projects** resources page, choose the project that contains the model whose tag you want to view\.

1. In the navigation pane, under the project you previously chose, choose **Models**\.

1. In the **Models** section, choose the model whose tag you want to view\. 

1. On the model's details page, choose the **Tags** tab\. The tags are shown in **Tags** section\.

## Tagging models \(SDK\)<a name="tagging-model-sdk"></a>

You can use the AWS SDK to:
+ Add tags to a new model
+ Add tags to an existing model
+ List the tags attached to a model 
+ Remove tags from a model 

This section includes AWS CLI examples\. If you haven't installed the AWS CLI, see [Step 4: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)\.

### Adding tags to a new model \(SDK\)<a name="tagging-new-model-sdk"></a>

You can add tags to a model when you create it using the [CreateModel](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_CreateModel) operation\. Specify one or more tags in the `Tags` array input parameter\. 

```
aws lookoutvision create-model --project-name "project name"\
  --output-config '{ "S3Location": { "Bucket": "output bucket", "Prefix":  "output folder" } }'\
  --tags '[{"Key":"Key","Value":"Value"}]'
```

For information about creating and training a model, see [Training a model \(SDK\)](model-train.md#create-model-sdk)\.

### Adding tags to an existing model \(SDK\)<a name="tagging-new-model-sdk"></a>

To add one or more tags to an existing model, use the [TagResource](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_TagResource) operation\. Specify the model's Amazon Resource Name \(ARN\) \(`ResourceArn`\) and the tags \(`Tags`\) that you want to add\. 

```
aws lookoutvision tag-resource --resource-arn "resource-arn"\
  --tags '[{"Key":"Key","Value":"Value"}]'
```

### Listing model tags \(SDK\)<a name="listing-model-tags-sdk"></a>

To list the tags attached to a model, use the [ListTagsForResource](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_ListTagsForResource) operation and specify the model's Amazon Resource Name \(ARN\), the \(`ResourceArn`\)\. The response is a map of tag keys and values that are attached to the specified model\.

```
aws lookoutvision list-tags-for-resource --resource-arn resource-arn
```

To see which models in a project have a specific tag, call `ListModels` to get a list of models\. Then call `ListTagsForResource` for each model in the response from `ListModels`\. Inspect the response from `ListTagsForResource` to see if the required tag is present\. 

### Removing tags from a model \(SDK\)<a name="removing-a-tag-sdk"></a>

To remove one or more tags from a model, use the [UntagResource](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_UntagResource) operation\. Specify the model's Amazon Resource Name \(ARN\) \(`ResourceArn`\) and the tag keys \(`Tag-Keys`\) that you want to remove\. 

```
aws lookoutvision untag-resource --resource-arn resource-arn\
  --tag-keys '["Key"]'
```