# Improving your model<a name="improve"></a>

During training Lookout for Vision tests your model with the test dataset and uses the results to create performance metrics\. You can use performance metrics to evaluate the performance of your model\. If necessary, you can take steps to improve your datasets and then retrain your model\.

## Step 1: Evaluate the performance of your model<a name="evaluate-model"></a>

You can access the performance metrics from the console and from the `DescribeModel` operation\. Amazon Lookout for Vision provides summary performance metrics for the test dataset and the predicted results for all individual images\. 

To view the performance metrics and test image predictions in the console, see [Viewing performance metrics \(console\)](performance-metrics.md#performance-metrics-console)\. For information about accessing performance metrics and test image predictions with the `DescribeModel` operation, see [Viewing performance metrics \(SDK\)](performance-metrics.md#performance-metrics-sdk)\.

Amazon Lookout for Vision provides the following summary performance metrics:
+ [Precision](#precision-metric)
+ [Recall](#recall-metric)
+ [F1 score](#f1-metric)

During testing, the model predicts a result for each test image in the test dataset\. The result for each prediction is compared to the label \(normal or anomaly\) of the corresponding test image as follows: 
+ Correctly predicting that an image is anomalous is considered a *true positive*\.
+ Incorrectly predicting that an image is anomalous is considered a *false positive*\.
+ Correctly predicting that an image is normal is considered a *true negative*\.
+ Incorrectly predicting that an image is normal is considered a *false negative*\.

Amazon Lookout for Vision uses the results of the comparisons to generate the performance metrics\.

### Precision<a name="precision-metric"></a>

The precision metric answers the question – *When the model predicts that an image contains an anomaly, how often is that prediction correct?*

Precision is a useful metric for situations where the cost of a false positive is high\. For example, the cost of removing a machine part that is not defective from an assembled machine\.

Amazon Lookout for Vision provides a summary precision metric value for the entire test dataset\. 

*Precision* is the fraction of correctly predicted anomalies \(true positives\) over all predicted anomalies \(true and false positives\)\. The formula for precision is as follows\.

*Precision value = true positives / \(true positives \+ false positives\)*

The possible values for precision range from 0–1\. The Amazon Lookout for Vision console displays precision as a percentage value \(0–100\)\.

A higher precision value indicates that more of the predicted anomalies are correct\. For example, suppose your model predicts that 100 images are anomalous\. If 85 of the predictions are correct \(the true positives\) and 15 are incorrect \(the false positives\), the precision is calculated as follows:

85 *true positives* / \(85 *true positives* \+ 15 *false positives*\) = **0\.85** precision value

However, if the model only predicts 40 images correctly out of 100 anomaly predictions, the resulting precision value is lower at **0\.40** \(that is, 40 / \(40 \+ 60\) = 0\.40\)\. In this case, your model is making more incorrect predictions than correct predictions\. To fix this, consider making improvements to your model\. For more information, see [Step 2: Improve your model](#improve-model)\.

For more information, see [Precision and recall](https://en.wikipedia.org/wiki/Precision_and_recall)\.

### Recall<a name="recall-metric"></a>

The recall metric answers the question \- *Of the total number of anomalous images in the test dataset, how many are correctly predicted as anomalous?*

The recall metric is useful for situations where the cost of a false negative is high\. For example, when the cost of not removing a defective part is high\. Amazon Lookout for Vision provides a summary recall metric value for the entire test dataset\. 

*Recall* is the fraction of the anomalous test images that were detected correctly\. It is a measure of how often the model can correctly predict an anomalous image, when it's actually present in the images of your test dataset\. The formula for recall is calculated as follows:

*Recall value = true positives / \(true positives \+ false negatives\) *

The range for recall is 0–1\. The Amazon Lookout for Vision console displays recall as a percentage value \(0–100\)\.

A higher recall value indicates that more of the anomalous images are correctly identified\. For example, suppose the test dataset contains 100 anomalous images\. If the model correctly detects 90 of the 100 anomalous images, then the recall is as follows:

90 *true positives* / \(90 *true positives* \+ 10 *false negatives*\) = **0\.90** recall value

A recall value of 0\.90 indicates that your model is correctly predicting most of the anomalous images in the test dataset\. If the model only predicts 20 of the anomalous images correctly, the recall is lower at **0\.20** \(that is, 20 / \(20 \+ 80\) = 0\.20\)\. 

In this case, you should consider making improvements to your model\. For more information, see [Step 2: Improve your model](#improve-model)\.

For more information, see [Precision and recall](https://en.wikipedia.org/wiki/Precision_and_recall)\.

### F1 score<a name="f1-metric"></a>

Amazon Lookout for Vision provides an average model performance score for the test dataset\. Specifically, model performance for anomaly classification is measured by the F1 score metric, which is the harmonic mean of the precision and recall scores\. 

*F1 score* is an aggregate measure that takes into account both precision and recall\. The model performance score is a value between 0 and 1\. The higher the value, the better the model is performing for both recall and precision\. For example, for a model with precision of 0\.9 and a recall of 1\.0, the F1 score is 0\.947\.

If the model isn't performing well, for example, with a low precision of 0\.30 and a high recall of 1\.0, the F1 score is 0\.46\. Similarly, if the precision is high \(0\.95\) and the recall is low \(0\.20\), the F1 score is 0\.33\. In both cases, the F1 score is low, which indicates problems with the model\. 

For more information, see [F1 score](https://en.wikipedia.org/wiki/F1_score)\.

## Step 2: Improve your model<a name="improve-model"></a>

The performance metrics might show that you can improve your model\. For example, if the model doesn't detect all anomalies in the test dataset, your model has low recall \(that is, the recall metric has a low value\)\. If you need to improve your model, consider the following:
+ Check that the training and test dataset images are properly labeled\.
+ Reduce the variability of image capture conditions such as lighting and object pose, and train your model on objects of the same type\.
+ Ensure that your images show only the required content\. For example, if your project detects anomalies in machine parts, make sure that no other objects are in your images\.
+ Add more labeled images to your train and test datasets\. If your test dataset has excellent recall and precision but the model performs poorly when deployed, your test dataset might not be representative enough and you need to extend it\.
+ If your test dataset results in poor recall and precision, you might need to add more training images\. For more information, see [Adding more images](edit-dataset.md#add-more-images-dataset)\. An alternative way to add labeled images to your training dataset is to run a trial detection task and verify the results\. You can then add the verified images to the training dataset\. For more information, see [Verifying your model with a trial detection task](trial-detection.md)\. 
+ Ensure you have sufficiently diverse normal and anomalous images in your training and test dataset\. The images must represent the type of normal and anomalous images that your model will encounter\. For example, when analyzing circuit boards, your normal images should represent the variations in position and soldering of components, such as resistors and transistors\. The anomalous images should represent the different types of anomalies that the system might encounter, such as misplaced or missing components\. 

After you have improved your training and test dataset, retrain and re\-evaluate your model\. For more information, see [Training your model](model-train.md)\.

If the metrics show that you model has acceptable performance, you can verify its performance by adding the results of a trial detection task to the test dataset\. After retraining, the performance metrics should confirm the performance metrics from the previous training\. For more information, see [Verifying your model with a trial detection task](trial-detection.md)\.