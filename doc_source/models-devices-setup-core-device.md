# Setting up your AWS IoT Greengrass Version 2 core device<a name="models-devices-setup-core-device"></a>

Amazon Lookout for Vision uses AWS IoT Greengrass Version 2 to simplify the deployment of the model component, Amazon Lookout for Vision Edge Agent component, and client application component to your AWS IoT Greengrass V2 core device\. For information about the devices and hardware that you can use, see [AWS IoT Greengrass Version 2 core device requirements](models-devices-setup-requirements.md)\.   

## Setting up your core device<a name="models-devices-setup-core-device-set-up"></a>

Use the following information to set up your core device\.

**To set up your core device**

1. Set up your GPU libraries\. Don't do this step if you aren't using GPU accelerated inference\. 

   1. Verify that you have a GPU that supports CUDA\. For more information, see [Verify You Have a CUDA\-Capable GPU](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#verify-you-have-cuda-enabled-system)\.

   1. Setup CUDA, cuDNN, and TensorRT on your device by doing one of the following: 
      + If you are using a Jetson device, install JetPack version 4\.4, 4\.5 or 4\.5\.1\. For more information, see [JetPack Archive](https://developer.nvidia.com/embedded/jetpack-archive)\.
      + If you are using x86 based hardware, and your NVIDIA GPU microarchitecture is prior to Ampere \(compute capability is less than 8\.0\), do the following:

        1. Set up CUDA version 10\.2 by following the instructions at [NVIDIA CUDA Installation Guide for Linux]( https://docs.nvidia.com/cuda/archive/10.2/cuda-installation-guide-linux/index.html)\.

        1. Install cuDNN, by following the instructions at [NVIDIA cuDNN Documentation](https://docs.nvidia.com/deeplearning/cudnn/install-guide/index.html)\. 

        1. Set up TensorRT \(version 7\.1\.3 or later, but earlier than 8\.0\.0\) by following the instructions at [NVIDIA TENSORRT DOCUMENTATION](https://docs.nvidia.com/deeplearning/tensorrt/install-guide/index.html)\. 
      + If you are using x86 based hardware, and your NVIDIA GPU microarchitecture is Ampere \(compute capability is 8\.0\), do the following:

        1. Set up CUDA \(version 11\.2\) by following the instructions at [NVIDIA CUDA Installation Guide for Linux](https://docs.nvidia.com/cuda/archive/11.2.0/cuda-installation-guide-linux/index.html)\. 

        1. Install cuDNN, by following the instructions at [NVIDIA cuDNN Documentation](https://docs.nvidia.com/deeplearning/cudnn/install-guide/index.html)\. 

        1. Set up TensorRT \(version 8\.2\.0\) by following the instructions at [NVIDIA TENSORRT DOCUMENTATION](https://docs.nvidia.com/deeplearning/tensorrt/install-guide/index.html)\. 

   1. Install the AWS IoT Greengrass Version 2 core software on your core device\. For more information, see [Install the AWS IoT Greengrass Core software](https://docs.aws.amazon.com/greengrass/v2/developerguide/getting-started.html#install-greengrass-v2) in the *AWS IoT Greengrass Version 2 Developer Guide*\. 

   1. To read from the Amazon S3 bucket that stores the model, attach permission to the IAM role \(token exchange role\) that you create during AWS IoT Greengrass Version 2 setup\. For more information, see [Allow access to S3 buckets for component artifacts](https://docs.aws.amazon.com/greengrass/v2/developerguide/device-service-role.html#device-service-role-access-s3-bucket)\.

1. At the command prompt, enter the folllowing command to install Python and a Python virtual environment onto the core device\.

   ```
   sudo apt install python3.8 python3-venv python3.8-venv
   ```

1. Use the following command to add the Greengrass user to the video group\. This lets Greengrass deployed components access the GPU:

   ```
   sudo usermod -a -G video ggc_user
   ```

1. \(Optional\) If you want to call Lookout for Vision Edge Agent API from a different user, add the required user to the `ggc_group`\. This lets the user communicate with the Lookout for Vision Edge Agent over the Unix Domain socket:

   ```
   sudo usermod -a -G ggc_group $(whoami)
   ```