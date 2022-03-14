# AWS IoT Greengrass Version 2 core device requirements<a name="models-devices-setup-requirements"></a>

To use an Amazon Lookout for Vision model on an AWS IoT Greengrass Version 2 core device, your model has various requirements of the core device\.

**Topics**
+ [Tested devices, chip architectures, and operating systems](#models-devices-setup-core-device-tested)
+ [Core device memory and storage](#models-devices-setup-memory-storage)
+ [Required software](#models-devices-software)

## Tested devices, chip architectures, and operating systems<a name="models-devices-setup-core-device-tested"></a>

We expect Amazon Lookout for Vision to work on the following hardware:
+ CPU architectures
  + X86\_64 \(64\-bit version of the x86 instruction set\)
  + ARM\_64 \(ARMv8 64\-bit CPU\)
+ NVIDIA GPU Accelerator with sufficient memory capacity \(At least 6\.0 GB for a running model\)\.

The Amazon Lookout for Vision team has tested Lookout for Vision models on the following devices, chip architectures, and operating systems\. 

### Devices<a name="models-devices-setup-core-device-tested-devices"></a>


| Device | Operating system | Architecture | Accelerator | Compiler options | 
| --- | --- | --- | --- | --- | 
|  jetson\_xavier \([NVIDIA® Jetson AGX Xavier](https://www.nvidia.com/en-us/autonomous-machines/embedded-systems/jetson-agx-xavier/)\)  |  Linux  |  [ARM\_64](https://en.wikipedia.org/wiki/AArch64)  |  NVIDIA  |  `{'gpu-code': 'sm_72', 'trt-ver': '7.1.3', 'cuda-ver': '10.2'}`  | 
|  g4dn\.xlarge \([EC2 Instances \(G4\) with NVIDIA T4 Tensor Core GPUs](https://aws.amazon.com/blogs/aws/now-available-ec2-instances-g4-with-nvidia-t4-tensor-core-gpus/)\)  |  Linux  |  [X86\_64/X86\-64](https://en.wikipedia.org/wiki/X86-64)  |  NVIDIA  |  `{'gpu-code': 'sm_75', 'trt-ver': '7.1.3', 'cuda-ver': '10.2'}`  | 

## Core device memory and storage<a name="models-devices-setup-memory-storage"></a>

To run a single model and the Amazon Lookout for Vision Edge Agent, your core device has the following memory and storage requirements\. You might need more memory and storage for your client application component\.
+ Storage – At least 1\.5 GB\.
+ Memory – At least 6\.0 GB for a running model\. 

## Required software<a name="models-devices-software"></a>

A core device requires the following software\.

### Jetson Devices<a name="models-devices-software-jetson"></a>

If your core device is a Jetson device, you need the following software installed on the core device\.


| Software | Supported versions | 
| --- | --- | 
|  Jetpack SDK  |   4\.4, 4\.5, or 4\.5\.1  | 
|  Python and Python virtual environment for Lookout for Vision Edge Agent version 0\.x  |  3\.7  | 
|  Python and Python virtual environment for Lookout for Vision Edge Agent version 1\.x  |  3\.8 or 3\.9  | 

### X86 hardware<a name="models-devices-software-x86"></a>

If your core device uses x86 hardware, you need the following software installed on the core device\.


| Software | Supported versions | 
| --- | --- | 
|  NVIDIA CUDA  |  10\.2  | 
|  NVIDIA TensorRT  |  At least 7\.1\.3 and less than 8\.0\.0  | 
|  Python and Python virtual environment for Lookout for Vision Edge Agent version 0\.x  |  3\.7  | 
|  Python and Python virtual environment for Lookout for Vision Edge Agent version 1\.x  |  3\.8 or 3\.9  | 