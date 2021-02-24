---
title: "How to install OpenCV 4.0 for Python3.6.5 on Ubuntu 18.04 LTS"
date: 2018-06-01T12:33:21+08:00
categories: ["development"]
draft: false
---
I know OpenCV 4.0.0 will rather be released on July 2018, but the installation procedure I describe here will not change. In fact, I already I installed and tested OpenCV 4.0.0-pre on Ubuntu 18.04 (to be continued soon)

I want to share my own experience regarding this procedure. Whenever I tried this or that tutorial related to installing a recent OpenCV version for Python3.x, I stumble on errors which are not recovered by those tutorials. So all what I advice you to do is to follow the steps mentioned in the official documentation<sup><a href="#1">1</a></sup> take your time to inform yourself about what you are doing and be meticilous about the details<sup><a href="#2">2</a></sup>

Ubuntu 18.04 LTS ships with Python3.6 version, which is also the only default Python version available<sup><a href="#3">3</a></sup>. Obviously you do not need to install some other Python3.x version unless for a specific reason.

As I said, all what you have to do is to reproduce the steps mentioned in the official documentation. But you will encounter few problems which all fall into the same category: the documentation refers to older libariries that are now renamed and upgraded. So whenever you try to install a given library and you get this error message `E: Unable to locate package some_package` then you have a short list of options to do respond to your issue:

1. Run a custom search on Ubuntu Packages Search which will be helpful in most cases<sup><a href="#4">4</a></sup>.
2. On Terminal, type `sudo apt-get install` followed by the libaray’s full or half name (start with the full name first) as written on the documentation, excluding the version numbering and press Tab for autocompletition or suggestions list from which you can easily guess what is the appropriate one.
3. Google about it and do not hesitate to use online communities to ask.
4. Last but not the least: do not even try to run `sudo apt-get install python-opencv`: this package is incomplete<sup><a href="#5">5</a></sup>.

#### Building OpenCV from source

Nothing beats installing a library from source, so let us follow the steps mentioned in the official documentation:
#### Required build dependencies
```cmd
sudo apt-get install cmake
sudo apt-get install python3.6-dev
sudo apt-get install python3-numpy
```
Note the documentation says `python-devel` but that is old, you should install `python3.6-dev` instead. Most packages suffixed by `-devel` are now re-named by `-dev` suffix. The truth is that it is always better to install Python related packages using pip which you can install by `sudo apt-get install python3-pip` and then install what you need as in this example `sudo pip3 install numpy` (note the difference when using `apt` above). But for the moment, let us continue with the Canonical instead of PyPI installation.

Do not run this command:
```cmd
sudo apt-get install build-essential
```
because those libraries are already available on Ubuntu 18.04.

Let us move to the next steps, but from now on, I will comment the obsolete software mentioned in the official documentation in front of the relevant command line:
```cmd
sudo apt-get install libgtk2.0-dev # gtk2-devel
sudo apt-get install libv4l-dev    # of libv4l-devel
sudo apt-get install ffmpeg        # of ffmpeg-devel
sudo apt-get install gstreamer1.0-plugins-base # gstreamer-plugins-base-devel
sudo apt-get install pkg-config libavcodec-dev libavformat-dev libswscale-dev
```
#### Optional Dependencies

Although the following consists of additional dependencies, I chose to install them instead of facing further unexpected issues. I have 1000 GB, so this does not cost me anything to install them:
```cmd
sudo apt-get install libpng++-dev       # libpng-devel
sudo apt-get install libjpeg-turbo8-dev # libjpeg-turbo-devel
sudo apt-get install jasper      # jasper-devel
sudo apt-get install openexr            # openexr-devel
sudo apt-get install libtiff5 libtiff5-dev libtiff-tools # libtiff-devel
sudo apt-get install libwebp-dev        # libwebp-dev
sudo apt-get install  libtbb2 libtbb-dev libjpeg62-dev libpng16-dev libdc1394-22-dev
```
By now the required and most important optional dependencies are installed. It is time to install OpenCV 4.0.0 for Python 3.6.
What I commented refers to the equivalent libraries mentioned on the official documentation on how to install OpenCV for an older Ubuntu system and Python (2) version.

If you do not have git install it: `sudo apt-get install git`. Then clone the OpenCV’s and OpenCV contrib repositories:
```cmd
cd ~
git clone https://github.com/opencv/opencv.git
git clone https://github.com/opencv/opencv_contrib.git
```
Note that OpenCV and opencv_contrib must be of the same version. By default, pulling down the repositories as shown above does the job, otherwise you can check it yourself too (`git checkout`).
Building OpenCV from Source Using CMake

Before setting up the build, give the right path to opencv_contrib`. If it is in your home directory alongside with the OpenCV’s directory then you do not have to change what is below:
```cmd
cd ~/opencv
mkdir build
cd build
cmake -D CMAKE_BUILD_TYPE=RELEASE \
	-D CMAKE_INSTALL_PREFIX=/usr/local \
	-D INSTALL_C_EXAMPLES=ON \
	-D INSTALL_PYTHON_EXAMPLES=ON \
	-D OPENCV_EXTRA_MODULES_PATH=~/opencv_contrib/modules \
	-D BUILD_EXAMPLES=ON ..
```
When the process ends, *CMake* should recognize the Python3.6 interpreter as well as where `numpy` is as shown on my screenshot. Note that *CMake*, in my case, knows also about Python2.7 interpreter because I installed it myself (which thing I did not cover here, but you do not need to install it if you want simply to do what the title of this tutorial says) Always inside the *opencv/build directory*, run this command:
```cmd
make -j4
```
I wrote `-j4` because my processor has 4 cores. So you must modify this number to your needs. If you do not know the number of cores your processor has then simply run `nproc` on your Terminal session.

If everything is OK by now, you should be able to install OpenCV:
```cmd
sudo make install
```
Enjoy !
---

<a name="1"></a>
<sup>1</sup>.https://docs.opencv.org/3.4.1/d2/de6/tutorial_py_setup_in_ubuntu.html
<a name="2"></a>
<sup>2</sup>.https://docs.opencv.org/3.4.1/d7/d9f/tutorial_linux_install.html
<a name="3"></a>
<sup>3</sup>.https://wiki.ubuntu.com/Python/Python36Transition
<a name="4"></a>
<sup>4</sup>.https://packages.ubuntu.com/
<a name="5"></a><br/>
<sup>5</sup>.https://www.begueradj.com/installing-opencv-3.2.0-for-python-3.5.2-on-ubuntu-16.04.2-lts.html

