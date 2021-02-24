---
title: "Installing OpenCV 3.2.0 for Python 3.5.2 on Ubuntu 16.04.2 LTS"
date: 2017-01-29T12:33:21+08:00
categories: ["development"]
draft: false
---
You will learn how to install OpenCV 3.2.0 on Ubuntu 16.04.2 LTS to work with Python 3.5.2.

### Introduction

I just freshly finished the installation of Ubuntu 16.04.2 LTS on my new laptop, so I had to install the programming environments I need. As I am passionate of image processing and computer vision, I needed to re-install the latest version of OpenCV (3.2.0) to be able to use it with Python 3.5.2

The best way to work with Python libraries on a Linux platform is by relying on Python virtual environments which saves headaches caused by versions dependencies. This is why I suppose you have already installed `virtualenv`.

### Nota bene

Do not even give a try to install the wrapper package for OpenCV python bindings:
```awk
pip install opencv-python
```
The reason is that this easy way of doing things will lead you to install a package which is not enough mature as you can not even deal with videos and can not even display an image using `cv2.imshow()` function. So do not be lazy and follow the below steps.

### Installation steps


I am sharing with you what worked for me on my machine (64 bits):


- Download OpenCV 3.2.0:
```c
wget -O 3.2.0.zip https://github.com/Itseez/opencv/archive/3.2.0.zip
unzip 3.2.0.zip
```

- Download SIFT and SURF OpenCV 3.2.0 implementations:
```c
wget -O opencv_contrib.zip https://github.com/Itseez/opencv_contrib/archive/3.2.0.zip
unzip opencv_contrib.zip
```

- Install the all the packages needed to compile a debian package:
```c
sudo apt-get update
sudo apt-get upgrade
sudo apt-get dist-upgrade
sudo apt-get install build-essential cmake pkg-config
```

- Install the libraries needed to process different video and images formats:
```c
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev
sudo apt-get install libxvidcore-dev libx264-dev libgtk-3-dev
sudo apt-get install libjpeg8-dev libtiff5-dev libjasper-dev libpng12-dev
```

- Install computation libraries:

```c
sudo apt-get install libatlas-base-dev numpy gfortran
```

- Install Python 3.5 development libraries:

```c
sudo apt-get install python3.5-dev
```

- Create a virtual environment where to install OpenCV 3.2.0:
```c
virtualenv cv
cd cv
source bin/activate
```

- Setting OpenCV 3.2.0 (this takes time, be patient):
```c
cd ~/3.2.0/
mkdir build
cd build
cmake -D CMAKE_BUILD_TYPE=RELEASE \
    -D CMAKE_INSTALL_PREFIX=/usr/local \
    -D INSTALL_PYTHON_EXAMPLES=ON \
    -D INSTALL_C_EXAMPLES=OFF \
    -D OPENCV_EXTRA_MODULES_PATH=~/opencv_contrib-3.1.0/modules \
    -D PYTHON_EXECUTABLE=~/.virtualenvs/cv/bin/python \
    -D BUILD_EXAMPLES=ON ..
```

- Compiling and installing OpenCV 3.2.0:
```c
make -j4
sudo make install
sudo ldconfig
```

- Rename `cv2.cpython-35m-x86_64-linux-gnu.so` to `cv2.so`
```c
sudo mv /usr/local/lib/python3.5/site-packages/cv2.cpython-35m-x86_64-linux-gnu.so /usr/local/lib/python3.5/site-packages/cv2.so
```

- Simlinking OpenCV3.2.0 to the `cv` virtual environment:
```c
cd ~/.virtualenvs/cv/lib/python3.5/site-packages/
ln -s /usr/local/lib/python3.5/site-packages/cv2.so cv2.so
```

These previous steps lead me to what I wanted to get:
```python
(cv) begueradj@h4ck-B1ll:~$ python
Python 3.5.2 (default, Nov 17 2016, 17:05:23) 
[GCC 5.4.0 20160609] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import cv2
>>> cv2.__version__
'3.2.0'
>>> 
```



