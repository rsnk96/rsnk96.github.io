---
layout: post
title:  "Updated OpenCV Installation in Linux (Anaconda / Default Python)"
date:   2017-02-23
desc: "Installing OpenCV on Ubuntu {works even with Anaconda Python}"
keywords: "python,ubuntu,anaconda,opencv,linux"
categories: [ComputerVision]
tags: [ComputerVision,OpenCV,Python,Linux,Anaconda]
icon: icon-centos
---

In my earlier blog posts, I talk about adopting different methods for installing OpenCV if you use Anaconda Python. Well, I was an idiot, because I was just providing a jugaad solution. Turns out you can have a unified solution, and that is exactly what I have done [here](https://github.com/rsnk96/Ubuntu-Setup-Scripts/blob/master/opencvDirectInstall.sh). Use that link if you quickly want to get around knowing how it works, or if you're simply too lazy.

If at any point of time, you are re-building with or without changed cmake parameters, then make sure you remove partial built files

```bash
$ sudo rm -rf opencv/build/ opencv/CMakeLists.txt opencv/cmake/
$ cd opencv
$ git reset --hard HEAD
$ cd ..
```

<br>

## Step 1: Download the appropriate scripts

In short, it will suffice if you download the following two scripts (preparatory and building), give the appropriate permissions with

```bash
$ chmod 755 4\ -\ opencvPreReq.sh
$ chmod 755 5\ -\ opencvInstall.sh 
```

Place both the scripts in the parent folder of where you would like OpenCV to be installed


<br>

## Step 2: Prepare the concoction
Run the preparatory file first

```bash
$ ./4\ -\ opencvPreReq.sh
```

Now, before running the actual building script, note that you MUST have anaconda removed from the environment PATH variable as otherwise, it detects a partial Conda-compliant version of QT5, which will lead to the final built openCV being unusable. Most likely, conda will be added to your shell starting code (.bashrc or .zshrc, for instance). After you have done that, 

```bash
$ ./5\ -\ opencvInstall.sh
```

<br>

## Step 3: Configuring CMAKE using CCMAKE

After the initial configuration, a ccmake prompt will come up, which lets you change the cmake parameters. As you have removed Anaconda Python from your PATH variable, it will not be detected by CMAKE. Hence, you have to manually input the location of your python variables (REFER: Images below. Replace /home/rsnk96/anaconda3 with your installation path). Press enter, and finish the building

<!--![instructions-1](/static/img/blog/opencv-anaconda-install/opencv Anaconda Install.png)-->
<img src='/static/img/blog/opencv-anaconda-install/opencv Anaconda Install.png' alt='instructions1' style="width: 1000px">
<br>
<br>
<img src='/static/img/blog/opencv-anaconda-install/opencv Anaconda Install-2.png' alt='instructions2' style="width: 1000px">

You might additionally face the problem of mismatched libtiff when building. If that is the case, remove libtiff from your system, use conda remove to remove libtiff from all your anaconda environments, and then run the shell script, which will build libtiff by itself

<br>

## Step 4: Make and Install

Aaah you really don't have to do anything after this. Just let the script do its job :-D

<br>

## Notes

* If you face the error `fatal error: ft2build.h: No such file or directory`, it is most likely because you have freetype enabled in contrib. Disable it using ` -DBUILD_opencv_freetype=OFF ..`


<br>
<br>
If you face any problem not mentioned here, feel free to ping me :-)