# Model requirements<a name="models-devices-setup-requirements"></a>

To use an Amazon Lookout for Vision model on an AWS IoT Greengrass Version 2 core device, your model has various requirements of the core device\.

For information about tested configurations, see [Tested devices, chip architectures, and operating systems](#models-devices-setup-core-device-tested)\. We also expect Amazon Lookout for Vision to work on any hardware with one of the following CPU architectures: 
+ X86\_64 \(64\-bit version of the x86 instruction set\)
+ ARM\_64 \(ARMv8 64\-bit CPU\)

You also need an NVIDIA GPU Accelerator with sufficient memory capacity \(At least 6\.0 GB for a running model\)\.

**Topics**
+ [Tested devices, chip architectures, and operating systems](#models-devices-setup-core-device-tested)
+ [Core device memory and storage](#models-devices-setup-memory-storage)

## Tested devices, chip architectures, and operating systems<a name="models-devices-setup-core-device-tested"></a>

The Amazon Lookout for Vision team has tested Lookout for Vision models on the following devices, chip architectures, and operating systems\. 

### Devices<a name="models-devices-setup-core-device-tested-devices"></a>


| Device | Operating system | Architecture | Accelerator | Compiler options example | 
| --- | --- | --- | --- | --- | 
|  jetson\_xavier \([NVIDIA® Jetson AGX Xavier](https://www.nvidia.com/en-us/autonomous-machines/embedded-systems/jetson-agx-xavier/)\)  |  Linux  |  [ARM\_64](https://en.wikipedia.org/wiki/AArch64)  |  NVIDIA  |  `{'gpu-code': 'sm_72', 'trt-ver': '5.1.6', 'cuda-ver': '10.0'}`  | 
|  g4dn\.xlarge \([EC2 Instances \(G4\) with NVIDIA T4 Tensor Core GPUs](https://aws.amazon.com/blogs/aws/now-available-ec2-instances-g4-with-nvidia-t4-tensor-core-gpus/)\)  |  Linux  |  [X86\_64/X86\-64](https://en.wikipedia.org/wiki/X86-64)  |  NVIDIA  |  `{'gpu-code': 'sm_75', 'trt-ver': '7.1.3', 'cuda-ver': '10.2'}`  | 

### Chip architectures<a name="models-devices-setup-core-device-tested-architectures"></a>

The Lookout for Vision team has tested models on an Nvidia GPU with the following:
+ Operating system: Linux
+ Architectures: [X86\_64/X86\-64](https://en.wikipedia.org/wiki/X86-64) \(64\-bit version of the x86 instruction set\) and [ARM\_64](https://en.wikipedia.org/wiki/AArch64)\(ARMv8 64\-bit CPU\)\. 

## Core device memory and storage<a name="models-devices-setup-memory-storage"></a>

To run a single model and the Amazon Lookout for Vision Edge Agent, your core device has the following memory and storage requirements\. You might need more memory and storage for your client application component\.
+ Storage – At least 1\.5 GB\.
+ Memory – At least 6\.0 GB for a running model\. 