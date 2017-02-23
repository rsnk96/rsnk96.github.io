---
layout: post
title:  "OpenCV Installation in Linux (Anaconda / Default Python) {Updated}"
date:   2017-02-23
desc: "Installing OpenCV on Ubuntu {works even with Anaconda Python}"
keywords: "python,ubuntu,anaconda,opencv,linux"
categories: [ComputerVision]
tags: [ComputerVision,OpenCV,Python,Linux,Anaconda]
icon: icon-centos
---

In my earlier blog posts, I talk about adopting different methods for installing OpenCV if you use Anaconda Python. Well, I was an idiot, because I was just providing a jugaad solution. Turns out you can have a unified solution, and that is exactly what I have done [here](https://github.com/rsnk96/Ubuntu-Setup-Scripts/blob/master/opencvDirectInstall.sh). Use that link if you quickly want to get around knowing how it works, or if you're simply too lazy.

This blog post explains what I have done to build a unified installation script.

## NOTE:

If at any point of time, you are re-building with or without changed cmake parameters, then make sure you remove partial built files

```bash
$ sudo rm -rf opencv/build/ opencv/CMakeLists.txt opencv/cmake/
$ cd opencv
$ git reset --hard HEAD
$ cd ..
```

<br>

## Step 1: Install the dependencies

Well, the first step towards having a complete installation is obviously to install the required dependencies. This is exactly what about a third of the code does

It installs libraries that would enable you to harness the full functionality of OpenCV (excluding a few modules in contrib like SFM and Kinect Support).

If Anaconda Python is not installed, then we install numpy, scipy and matplotlib for Python 2 and 3 using the Ubuntu software centre. If it is, then we simply check if they are installed using pip directly


## Step 2: Download the OpenCV and OpenCV Contrib Source code

It first checks if the folder already exists (in case you have partially run the script in the past). If not, it firsts changes your git buffer size (as the opencv repo size might be bigger than the default maximum git buffer size), and then clones both the source code repos. In case the folder already exists, it simply updates updates by pulling the latest changes from the repository


## Step 3: Extracting necessary Python program parameters

The main cause of conflict / issue when you have Anaconda Python installed is the fact that Anaconda has a malformed MKL library and it's own versions of lots of other libraries, which the OpenCV cmake detects, and uses to override the default full version of the library that exists in your system. To prevent that, we need to remove Anaconda from the `PATH` variable, so that it is not detected at all by CMAKE. However, this would mean that Anaconda Python will also not be detected. Hence, we find the all the Python directories needed by CMAKE before hand, and then remove all traces of Anaconda from the path using 
```bash
export PATH=$(echo $PATH | tr ':' '\n' | grep -v "conda[2-9]\?" | uniq | tr '\n' ':')
```

**NOTE**: If you do not have Anaconda Python, this will still work perfectly alright

## Step 4: CMAKE parameters

We enforce the usage of the default Python by storing the Python paths needed for the installation (before removing Anaconda from the path using the above command) by executing
```bash
py2Ex=`which python2`
py2In=`python2 -c "from distutils.sysconfig import get_python_inc; print(get_python_inc())"`
py2Pack=`python2 -c "from distutils.sysconfig import get_python_lib; print(get_python_lib())"`
py3Ex=`which python3`
py3In=`python3 -c "from distutils.sysconfig import get_python_inc; print(get_python_inc())"`
py3Pack=`python3 -c "from distutils.sysconfig import get_python_lib; print(get_python_lib())"`
```
Of course, Python2 installation will be needed after 2019. We next use these variables to force CMAKE to use these directories only
```bash
 -DPYTHON2_EXECUTABLE=$py2Ex \
 -DPYTHON2_INCLUDE_DIR=$py2In \
 -DPYTHON2_PACKAGES_PATH=$py2Pack \
 -DPYTHON3_EXECUTABLE=$py3Ex \
 -DPYTHON3_INCLUDE_DIR=$py3In \
 -DPYTHON3_PACKAGES_PATH=$py3Pack \
```

We next enable TBB (Threaded Building blocks, gives higher performance), V4L (Video library) and QT (Better image displayer)
```bash
 -DWITH_TBB=ON \
 -DWITH_V4L=ON \
 -DWITH_QT=ON \
```

We also explicitly disable CUDA, as compiling OpenCV with CUDA slows down the process significantly
```bash
 -DWITH_CUDA=OFF \
```

We build TIFF as OpenCV relies on an older version of libtiff, and using the system default libtiff in Ubunttu 14.04+ will throw errors.
```bash
 -DBUILD_TIFF=ON \
```

## Step 5
We finally make and install, and the installation should complete. We then re-add Anaconda to the PATH variable.

You can check out and download the whole script [here](https://github.com/rsnk96/Ubuntu-Setup-Scripts/blob/master/opencvDirectInstall.sh)