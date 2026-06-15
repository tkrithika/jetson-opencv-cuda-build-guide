<div align="center">

![OpenCV](https://img.shields.io/badge/OpenCV-5C3EE8?style=for-the-badge&logo=opencv&logoColor=white)
![CUDA](https://img.shields.io/badge/CUDA-76B900?style=for-the-badge&logo=nvidia&logoColor=white)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![cuDNN](https://img.shields.io/badge/cuDNN-76B900?style=for-the-badge&logo=nvidia&logoColor=white)
![CMake](https://img.shields.io/badge/CMake-064F8C?style=for-the-badge&logo=cmake&logoColor=white)
![NVIDIA Jetson](https://img.shields.io/badge/NVIDIA_Jetson-76B900?style=for-the-badge&logo=nvidia&logoColor=white)
![C++](https://img.shields.io/badge/C++-00599C?style=for-the-badge&logo=cplusplus&logoColor=white)
![Bash](https://img.shields.io/badge/Bash-4EAA25?style=for-the-badge&logo=gnubash&logoColor=white)


</div>

# OpenCV with CUDA Support on Jetson (Build from Source)

## Table of Contents

- [Overview](#overview)
- [System Preparation](#1-system-preparation)
- [Install Required Dependencies](#2-install-required-dependencies)
- [Download OpenCV Source Code](#3-download-opencv-source-code)
- [Create Build Directory](#4-create-build-directory)
- [Configure Build with CUDA](#5-configure-build-with-cuda-cmake)
- [Compile OpenCV](#6-compile-opencv)
- [Install OpenCV](#7-install-opencv)
- [Verify CUDA Support](#8-verify-cuda-support)
- [Runtime Verification](#9-runtime-verification-python)
- [Monitoring with jtop](#10-monitoring-with-jtop-optional)

  
## Overview

Pre-installed versions of OpenCV on Jetson devices often lack CUDA support.  
To enable GPU acceleration, OpenCV must be compiled from source with CUDA and cuDNN enabled.

This guide walks through the complete process of building OpenCV with CUDA support on a Jetson device.

---

## 1. System Preparation

Ensure your system is up to date and has sufficient storage (recommended: 6–8 GB free).

```bash
sudo apt-get update && sudo apt-get upgrade -y
```

Check available disk space:
```
df -h
```

## 2. Install Required Dependencies

Install all necessary libraries required for building OpenCV.

Important: Run the following command as a single block.
The backslashes (\) indicate line continuation.

```
sudo apt-get install -y build-essential cmake git unzip pkg-config \
    libjpeg-dev libpng-dev libtiff-dev \
    libavcodec-dev libavformat-dev libswscale-dev libv4l-dev \
    libxvidcore-dev libx264-dev \
    libgtk-3-dev libcanberra-gtk3-module \
    python3-dev python3-numpy \
    libtbb2 libtbb-dev \
    libopenblas-dev libatlas-base-dev gfortran \
    libhdf5-dev protobuf-compiler
```

## 3. Download OpenCV Source Code

Clone the official OpenCV repositories:

```
cd ~
git clone https://github.com/opencv/opencv.git
git clone https://github.com/opencv/opencv_contrib.git
```

## 4. Create Build Directory

```
cd ~/opencv
mkdir build
cd build
```

## 5. Configure Build with CUDA (CMake)

Configure the build with CUDA, cuDNN, and GPU acceleration enabled:

```
cmake -D CMAKE_BUILD_TYPE=RELEASE \
      -D CMAKE_INSTALL_PREFIX=/usr/local \
      -D OPENCV_EXTRA_MODULES_PATH=~/opencv_contrib/modules \
      -D ENABLE_NEON=ON \
      -D WITH_CUDA=ON \
      -D WITH_CUDNN=ON \
      -D OPENCV_DNN_CUDA=ON \
      -D CUDA_ARCH_BIN="7.2" \  
      -D WITH_TBB=ON \
      -D BUILD_opencv_python3=ON \
      -D BUILD_opencv_python2=OFF \
      -D WITH_QT=OFF \
      -D WITH_GTK=ON \
      -D WITH_OPENGL=ON \
      -D BUILD_EXAMPLES=OFF ..
```
Note: Update 'CUDA_ARCH_BIN' based on your jetson model

## 6. Compile OpenCV

> This step is resource-intensive and may take 1–2 hours.
> Ensure your device is properly cooled and powered.

```
make -j$(nproc)
```

## 7. Install OpenCV

```
sudo make install
sudo ldconfig
```

## 8. Verify CUDA Support

Check if OpenCV was successfully built with CUDA:

```
python3 -c "import cv2; print(cv2.getBuildInformation())"
```

Look for:


```text
CUDA: YES
cuDNN: YES
```

## 9. Runtime Verification (Python)

You can further confirm GPU availability using Python:

First in your terminal run:

```
cd ~
```

This returns you to the home directory.

Now start python:

```
python3
```

Then run:

```
import cv2
print(cv2.getBuildInformation())
print("CUDA Available:", cv2.cuda.getCudaEnabledDeviceCount() > 0)
```

Expected output:

CUDA Available: True

## 10. Monitoring with jtop (Optional)

To monitor GPU usage on Jetson:

```
sudo -H pip3 install -U jetson-stats
sudo reboot
```

After reboot:

```
jtop
```

This allows you to observe GPU utilization when running OpenCV applications.

## Result

If all steps are completed successfully, OpenCV will be fully accelerated using CUDA on your Jetson device.

## Who Is This For?

This guide is intended for:

- Developers working on NVIDIA Jetson platforms  
- Embedded systems engineers  
- Computer vision and AI/ML practitioners  
- Students and researchers building GPU-accelerated applications  

It is especially useful when the default OpenCV installation does not include CUDA support.

## Disclaimer

This project is an independent technical guide and is not affiliated with or endorsed by NVIDIA Corporation or the OpenCV project.

## License

This project is licensed under the MIT License.  






















