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
  + Aarch64 \(ARMv8 64\-bit CPU\)
+ \(GPU accelerated inference only\) NVIDIA GPU Accelerator with sufficient memory capacity \(At least 6\.0 GB for a running model\)\.

The Amazon Lookout for Vision team has tested Lookout for Vision models on the following devices, chip architectures, and operating systems\. 

### Devices<a name="models-devices-setup-core-device-tested-devices"></a>


| Device | Operating system | Architecture | Accelerator | Compiler options | 
| --- | --- | --- | --- | --- | 
|  jetson\_xavier \([NVIDIA® Jetson AGX Xavier](https://www.nvidia.com/en-us/autonomous-machines/embedded-systems/jetson-agx-xavier/)\)  |  Linux  |  [Aarch64](https://en.wikipedia.org/wiki/AArch64)  |  NVIDIA  |  `{"gpu-code": "sm_72", "trt-ver": "7.1.3", "cuda-ver": "10.2"}` `{"gpu-code": "sm_72", "trt-ver": "8.2.1", "cuda-ver": "10.2"}`  | 
|  g4dn\.xlarge \([EC2 Instances \(G4\) with NVIDIA T4 Tensor Core GPUs](https://aws.amazon.com/blogs/aws/now-available-ec2-instances-g4-with-nvidia-t4-tensor-core-gpus/)\)  |  Linux  |  [X86\_64/X86\-64](https://en.wikipedia.org/wiki/X86-64)  |  NVIDIA  |  `{"gpu-code": "sm_75", "trt-ver": "7.1.3", "cuda-ver": "10.2"}`  | 
|  g5\.xlarge \([EC2 Instances \(G5\) with NVIDIA A10G Tensor Core GPUs](https://aws.amazon.com/blogs/aws/new-ec2-instances-g5-with-nvidia-a10g-tensor-core-gpus/)\)  |  Linux  |  [X86\_64/X86\-64](https://en.wikipedia.org/wiki/X86-64)  |  NVIDIA  |  `{"gpu-code": "sm_80", "trt-ver": "8.2.0", "cuda-ver": "11.2"}`  | 
|  c5\.2xlarge \([Amazon EC2 C5 Instances\)](https://aws.amazon.com/ec2/instance-types/c5/)  |  Linux  |  [X86\_64/X86\-64](https://en.wikipedia.org/wiki/X86-64)  |  CPU  |  `{"mcpu": "core-avx2"}`  | 

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
|  Jetpack SDK  |  4\.4 to 4\.6\.1  | 
|  Python and Python virtual environment for Lookout for Vision Edge Agent version 1\.x  |  3\.8 or 3\.9  | 

### X86 hardware<a name="models-devices-software-x86"></a>

If your core device uses x86 hardware, you need the following software installed on the core device\. 

#### CPU inference<a name="models-devices-software-cpu-inference"></a>


| Software | Supported versions | 
| --- | --- | 
|   Python and Python virtual environment for Lookout for Vision Edge Agent version 1\.x  |  3\.8 or 3\.9  | 

#### GPU accelerated inference<a name="models-devices-software-gpu-inference"></a>

Software versions vary depending on the microarchitecture of the NVIDIA GPU that you use\.

##### NVIDIA GPU with microarchitecture prior to Ampere \(compute capability is less than 8\.0\)<a name="model-devices-software-x86-smaller-sm_80"></a>

Required software for an NVIDIA GPU with a microarchitecture prior to Ampere \(compute capability that is less than 8\.0\)\. The `gpu-code` must be less than `sm_80`\.


| Software | Supported versions | 
| --- | --- | 
|  NVIDIA CUDA  |  10\.2  | 
|  NVIDIA TensorRT  |  At least 7\.1\.3 and less than 8\.0\.0  | 
|  Python and Python virtual environment for Lookout for Vision Edge Agent version 1\.x  |  3\.8 or 3\.9  | 

##### NVIDIA GPU with Ampere microarchitecture \(compute capability 8\.0\)<a name="model-devices-software-x86-sm_80"></a>

Required software for an NVIDIA GPU with the Ampere microarchitecture \(compute capability is 8\.0\)\. The `gpu-code` must be `sm_80`\)\.


| Software | Supported versions | 
| --- | --- | 
|  NVIDIA CUDA  |  11\.2  | 
|  NVIDIA TensorRT  |  8\.2\.0  | 
|  Python and Python virtual environment for Lookout for Vision Edge Agent version 1\.x  |  3\.8 or 3\.9  | 