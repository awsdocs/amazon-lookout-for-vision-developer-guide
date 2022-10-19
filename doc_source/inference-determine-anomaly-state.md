# Determining if an image is anomalous<a name="inference-determine-anomaly-state"></a>

You can determine if an image is anomalous in a variety of ways\. The method you choose depends on your use case and the type of your model\. The following are potential solutions\.

**Topics**
+ [Classification](#inference-classification)
+ [Segmentation](#inference-segmentation)

## Classification<a name="inference-classification"></a>

 `IsAnomalous` classifies an image as anomalous, use the `Confidence` field to help decide if the image is actually anomalous\. A higher value indicates greater confidence\. For example, you might decide a product is defective only if the confidence is over 80%\. You can classify images analyzed by classification models or by image segmentation models\.

------
#### [ Python ]

For the complete code example, see [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/python/example_code/lookoutvision/inference.py)\.

```
    def reject_on_classification(image, prediction, confidence_limit):
        """
        Returns True if the anomaly confidence is greater than or equal to 
        the supplied confidence limit.
        :param image: The name of the image file that was analyzed.
        :param prediction: The DetectAnomalyResult object returned from DetectAnomalies
        :param confidence_limit: The minimum acceptable confidence. Float value between 0 and 1.
        :return: True if the error condition indicates an anomaly, otherwise False.
        """

        reject = False

        logger.info("Checking classification for %s", image)

        if prediction['IsAnomalous'] and prediction['Confidence'] >= confidence_limit:
            reject = True
            reject_info=(f"Rejected: Anomaly confidence ({prediction['Confidence']:.2%}) is greater"
                    f" than limit ({confidence_limit:.2%})")
            logger.info("%s", reject_info)

        if not reject:
            logger.info("No anomalies found.")
        return reject
```

------
#### [ Java V2 ]

```
    public static boolean rejectOnClassification(String image, DetectAnomalyResult prediction, float minConfidence) {
        /**
         * Rejects an image based on its anomaly classification and prediction
         * confidence
         * 
         * @param image         The file name of the analyzed image.
         * @param prediction    The prediction for an image analyzed with
         *                      DetectAnomalies.
         * @param minConfidence The minimum acceptable confidence for the prediction
         *                      (0-1).
         * 
         * @return boolean True if the image is anomalous, otherwise False.
         */

        Boolean reject = false;

        logger.log(Level.INFO, "Checking classification for {0}", image);

        String[] logParameters = { prediction.confidence().toString(), String.valueOf(minConfidence) };

        if (Boolean.TRUE.equals(prediction.isAnomalous()) && prediction.confidence() >= minConfidence) {
            logger.log(Level.INFO, "Rejected: Anomaly confidence {0} is greater than confidence limit {1}",
                    logParameters);
            reject = true;
        }
        if (Boolean.FALSE.equals(reject))
            logger.log(Level.INFO, ": No anomalies found.");

        return reject;

    }
```

------

## Segmentation<a name="inference-segmentation"></a>

If your model is an image segmentation model, you can use the segmentation information to determine if an image contains anomalies\. You can also use an image segmentation model to classify images\. 

### Area of anomaly<a name="inference-segmentation-area"></a>

Use the percentage coverage \(`TotalPercentageArea`\) of an anomaly on the image\. For example, you might decide a product is defective if the area of an anomaly is greater than 1% of the image\. 

------
#### [ Python ]

For the complete code example, see [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/python/example_code/lookoutvision/inference.py)\.

```
    def reject_on_coverage(image, prediction, confidence_limit, anomaly_label, coverage_limit):
        """
        Checks if the coverage area of an anomaly is greater than the coverage limit and if
        the prediction confidence is greater than the confidence limit.
        :param image: The name of the image file that was analyzed.
        :param prediction: The DetectAnomalyResult object returned from DetectAnomalies
        :param confidence_limit: The minimum acceptable confidence (float 0-1).
        :anomaly_label: The anomaly label for the type of anomaly that you want to check.
        :coverage_limit: The maximum acceptable percentage coverage of an anomaly (float 0-1).
        :return: True if the error condition indicates an anomaly, otherwise False.
        """

        reject = False

        logger.info("Checking coverage for %s", image)

        if prediction['IsAnomalous'] and prediction['Confidence'] >= confidence_limit:
            for anomaly in prediction['Anomalies']:
                if (anomaly['Name'] == anomaly_label and 
                        anomaly['PixelAnomaly']['TotalPercentageArea'] > (coverage_limit)):
                    reject = True
                    reject_info=(f"Rejected: Anomaly confidence ({prediction['Confidence']:.2%}) "
                        f"is greater than limit ({confidence_limit:.2%}) and {anomaly['Name']} "
                        f"coverage ({anomaly['PixelAnomaly']['TotalPercentageArea']:.2%}) "
                        f"is greater than limit ({coverage_limit:.2%})")

                    logger.info("%s", reject_info)

        if not reject:
            logger.info("No anomalies found.")

        return reject
```

------
#### [ Java V2 ]

```
    public static Boolean rejectOnCoverage(String image, DetectAnomalyResult prediction, float minConfidence,
            String anomalyType, float maxCoverage) {
        /**
         * Rejects an image based on a maximum allowable coverage area for an anomaly
         * type.
         * 
         * @param image         The file name of the analyzed image.
         * @param prediction    The prediction for an image analyzed with
         *                      DetectAnomalies.
         * @param minConfidence The minimum acceptable confidence for the prediction
         *                      (0-1).
         * @param anomalyTypes  The anomaly type to check.
         * @param maxCoverage   The maximum allowable coverage area of the anomaly type.
         *                      (0-1).
         * 
         * @return boolean True if the coverage area of the anomaly type exceeds the
         *         maximum allowed, otherwise False.
         */

        Boolean reject = false;

        logger.log(Level.INFO, "Checking coverage for {0}", image);

        if (Boolean.TRUE.equals(prediction.isAnomalous()) && prediction.confidence() >= minConfidence) {
            for (Anomaly anomaly : prediction.anomalies()) {

                if (Objects.equals(anomaly.name(), anomalyType)
                        && anomaly.pixelAnomaly().totalPercentageArea() >= maxCoverage) {

                    String[] logParameters = { prediction.confidence().toString(),
                            String.valueOf(minConfidence),
                            String.valueOf(anomaly.pixelAnomaly().totalPercentageArea()),
                            String.valueOf(maxCoverage) };
                    logger.log(Level.INFO,
                            "Rejected: Anomaly confidence {0} is greater than confidence limit {1} and " +
                                    "{2} anomaly type coverage is higher than coverage limit {3}\n",
                            logParameters);
                    reject = true;

                }
            }
        }

        if (Boolean.FALSE.equals(reject))
            logger.log(Level.INFO, ": No anomalies found.");

        return reject;
    }
```

------

### Number of anomaly types<a name="inference-segmentation-number-of-anomaly-types"></a>

Use a count of different anomaly types \(`Name`\) found on the image\. For example, you might decide a product is defective if there is more than two types of anomaly present\. 

------
#### [ Python ]

For the complete code example, see [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/python/example_code/lookoutvision/inference.py)\.

```
    def reject_on_anomaly_types(image, prediction, confidence_limit, anomaly_types_limit):
        """
        Checks if the number of anomaly types is greater than than the anomaly types
        limit and if the prediction confidence is greater than the confidence limit.
        :param image: The name of the image file that was analyzed.
        :param prediction: The DetectAnomalyResult object returned from DetectAnomalies
        :param confidence: The minimum acceptable confidence. Float value between 0 and 1.
        :param anomaly_types_limit: The maximum number of allowable anomaly types (Integer).
        :return: True if the error condition indicates an anomaly, otherwise False.
        """

        logger.info("Checking number of anomaly types for %s",image)

        reject = False

        if prediction['IsAnomalous'] and prediction['Confidence'] >= confidence_limit:

            anomaly_types = {anomaly['Name'] for anomaly in prediction['Anomalies']\
                if anomaly['Name'] != 'background'}

            if len (anomaly_types) > anomaly_types_limit:
                    reject = True
                    reject_info = (f"Rejected: Anomaly confidence ({prediction['Confidence']:.2%}) "
                    f"is greater than limit ({confidence_limit:.2%}) and "
                    f"the number of anomaly types ({len(anomaly_types)-1}) is "
                    f"greater than the limit ({anomaly_types_limit})")

                    logger.info("%s", reject_info)

        if not reject:
            logger.info("No anomalies found.")
        return reject
```

------
#### [ Java V2 ]

```
    public static Boolean rejectOnAnomalyTypeCount(String image, DetectAnomalyResult prediction,
            float minConfidence, Integer maxAnomalyTypes) {

        /**
         * Rejects an image based on a maximum allowable number of anomaly types.
         *
         * @param image           The file name of the analyzed image.
         * @param prediction      The prediction for an image analyzed with
         *                        DetectAnomalies.
         * @param minConfidence   The minimum acceptable confidence for the predictio
         *                        (0-1).
         * @param maxAnomalyTypes The maximum allowable number of anomaly types.
         * 
         * @return boolean True if the image contains more than the maximum allowed
         *         anomaly types, otherwise False.
         */

        Boolean reject = false;

        logger.log(Level.INFO, "Checking coverage for {0}", image);

        Set<String> defectTypes = new HashSet<>();

        if (Boolean.TRUE.equals(prediction.isAnomalous()) && prediction.confidence() >= minConfidence) {
            for (Anomaly anomaly : prediction.anomalies()) {
                defectTypes.add(anomaly.name());
            }
            // Reduce defect types by one to account for 'background' anomaly type.
            if ((defectTypes.size() - 1) > maxAnomalyTypes) {
                String[] logParameters = { prediction.confidence().toString(),
                        String.valueOf(minConfidence),
                        String.valueOf(defectTypes.size()),
                        String.valueOf(maxAnomalyTypes) };
                logger.log(Level.INFO, "Rejected: Anomaly confidence {0} is >= minimum confidence {1} and " +
                        "the number of anomaly types {2} > the allowable number of anomaly types {3}\n", logParameters);
                reject = true;
            }

        }

        if (Boolean.FALSE.equals(reject))
            logger.log(Level.INFO, ": No anomalies found.");

        return reject;
    }
```

------