# Detect anomalies in an image with a trained Lookout for Vision model using an AWS SDK<a name="example_lookoutvision_DetectAnomalies_section"></a>

The following code example shows how to detect anomalies in an image with a trained Lookout for Vision model\.

**Note**  
The source code for these examples is in the [AWS Code Examples GitHub repository](https://github.com/awsdocs/aws-doc-sdk-examples)\. Have feedback on a code example? [Create an Issue](https://github.com/awsdocs/aws-doc-sdk-examples/issues/new/choose) in the code examples repo\. 

For more information, see [Detecting anomalies in an image](https://docs.aws.amazon.com/lookout-for-vision/latest/developer-guide/inference-detect-anomalies.html)\.

------
#### [ Python ]

**SDK for Python \(Boto3\)**  
 To learn how to set up and run this example, see [GitHub](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/python/example_code/lookoutvision#code-examples)\. 
  

```
class Inference:
    """
    Shows how to detect anomalies in an image using a trained Lookout for Vision model.
    """

    @staticmethod
    def detect_anomalies(lookoutvision_client, project_name, model_version, photo):
        """
        Calls DetectAnomalies using the supplied project, model version, and image.
        :param lookoutvision_client: A Lookout for Vision Boto3 client.
        :param project: The project that contains the model that you want to use.
        :param model_version: The version of the model that you want to use.
        :param photo: The photo that you want to analyze.
        :return: The DetectAnomalyResult object that contains the analysis results.
        """

        image_type = imghdr.what(photo)
        if image_type == "jpeg":
            content_type = "image/jpeg"
        elif image_type == "png":
            content_type = "image/png"
        else:
            logger.info("Image type not valid for %s", photo)
            raise ValueError(
                f"File format not valid. Supply a jpeg or png format file: {photo}")


        # Get images bytes for call to detect_anomalies.
        with open(photo, "rb") as image:
            response = lookoutvision_client.detect_anomalies(
                ProjectName=project_name,
                ContentType=content_type,
                Body=image.read(),
                ModelVersion=model_version)

        return response['DetectAnomalyResult']

    @staticmethod
    def download_from_s3(s3_resource, photo):
        """
        Downloads an image from an S3 bucket.

        :param s3_resource: A Boto3 Amazon S3 resource.
        :param photo: The Amazon S3 path of a photo to download.
        return: The local path to the downloaded file.
        """
        try:
            bucket, key = photo.replace("s3://", "").split("/", 1)
            local_file = os.path.basename(photo)
        except ValueError:
            logger.exception("Couldn't get S3 info for %s", photo)
            raise

        try:
            logger.info("Downloading %s", photo)
            s3_resource.Bucket(bucket).download_file(key, local_file)
        except ClientError:
            logger.exception("Couldn't download %s from S3.", photo)
            raise

        return local_file

    @staticmethod
    def reject_on_classification(image, prediction, confidence_limit):
        """
        Returns True if the anomaly confidence is greater than or equal to 
        the supplied confidence limit.
        :param image: The name of the image file that was analyzed.
        :param prediction: The DetectAnomalyResult object returned from DetectAnomalies.
        :param confidence_limit: The minimum acceptable confidence (float 0 - 1).
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

    @staticmethod
    def reject_on_anomaly_types(image, prediction, confidence_limit, anomaly_types_limit):
        """
        Checks if the number of anomaly types is greater than the anomaly types
        limit and if the prediction confidence is greater than the confidence limit.
        :param image: The name of the image file that was analyzed.
        :param prediction: The DetectAnomalyResult object returned from DetectAnomalies.
        :param confidence: The minimum acceptable confidence (float 0 - 1).
        :param anomaly_types_limit: The maximum number of allowable anomaly types (int).
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

    @staticmethod
    def reject_on_coverage(image, prediction, confidence_limit, anomaly_label, coverage_limit):
        """
        Checks if the coverage area of an anomaly is greater than the coverage limit and if
        the prediction confidence is greater than the confidence limit.
        :param image: The name of the image file that was analyzed.
        :param prediction: The DetectAnomalyResult object returned from DetectAnomalies.
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

    @staticmethod
    def analyze_image(lookoutvision_client, image, config):
        """
        Analyzes an image with an Amazon Lookout for Vision model. Also
        runs a series of checks to determine if the contents of an image
        should be rejected.
        :param lookoutvision_client: A Lookout for Vision Boto3 client.
        param image: A local image that you want to analyze.
        param config: Configuration information for the model and reject
        limits.
        """

        project = config['project']
        model_version = config['model_version']
        confidence_limit = config['confidence_limit']
        coverage_limit = config['coverage_limit']
        anomaly_types_limit = config['anomaly_types_limit']
        anomaly_label = config['anomaly_label']


        # Get analysis results.
        print(f"Analyzing {image}.")

        prediction = Inference.detect_anomalies(
            lookoutvision_client, project, model_version, image)

        anomalies = []

        reject = Inference.reject_on_classification(
            image, prediction, confidence_limit)

        if reject:
            anomalies.append("Classification: An anomaly was found.")

        reject = Inference.reject_on_coverage(
            image, prediction, confidence_limit, anomaly_label, coverage_limit)

        if reject:
            anomalies.append("Coverage: Anomaly coverage too high.")

        reject = Inference.reject_on_anomaly_types(
            image, prediction, confidence_limit, anomaly_types_limit)

        if reject:
            anomalies.append(
                "Anomaly type count: Too many anomaly types found.")
            print()

        if len(anomalies) > 0:
            print(f"Anomalies found in {image}")
            for anomaly in anomalies:
                print(f"{anomaly}")
        else:
            print(f"No anomalies found in {image}")


def main():
    """
    Detects anomalies in an image file.
    """
    try:
        logging.basicConfig(level=logging.INFO,
                            format="%(levelname)s: %(message)s")


        parser = argparse.ArgumentParser(
            description="Find anomalies with Amazon Lookout for Vision.")
        parser.add_argument(
            "image",
            help="The file that you want to analyze. Supply a local file path or a "
                 "path to an S3 object.")
        parser.add_argument(
            "config", help=("The configuration JSON file to use. "
            "See https://github.com/awsdocs/aws-doc-sdk-examples/blob/main/"
            "python/example_code/lookoutvision/README.md"))

        args = parser.parse_args()

        lookoutvision_client = boto3.client("lookoutvision")
        s3_resource = boto3.resource('s3')

        # Get configuration information.
        with open(args.config, encoding="utf-8")  as config_file:
            config = json.load(config_file)

        # Download image if located in S3 bucket.
        if args.image.startswith("s3://"):
            image = Inference.download_from_s3(s3_resource, args.image)
        else:
            image = args.image

        Inference.analyze_image(lookoutvision_client, image, config)

        # Delete image, if downloaded from S3 bucket.
        if args.image.startswith("s3://"):
            os.remove(image)

    except ClientError as err:
        print(f"Service error: {err.response['Error']['Message']}")
    except FileNotFoundError as err:
        print(f"The supplied file couldn't be found: {err.filename}.")
    except ValueError as err:
        print(f"A value error occurred: {err}.")
    else:
        print("\nSuccessfully completed analysis.")


if __name__ == "__main__":
    main()
```
+  For API details, see [DetectAnomalies](https://docs.aws.amazon.com/goto/boto3/lookoutvision-2020-11-20/DetectAnomalies) in *AWS SDK for Python \(Boto3\) API Reference*\. 

------

For a complete list of AWS SDK developer guides and code examples, see [Using this service with an AWS SDK](sdk-general-information-section.md)\. This topic also includes information about getting started and details about previous SDK versions\.