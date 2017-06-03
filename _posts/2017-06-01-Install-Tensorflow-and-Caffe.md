---
layout: post
title:  "Install tensorflow&Caffe in Ubuntu"
date:   2017-06-01 19:47:00 +0900
excerpt: "听说Deep Learning最近很火？"
---
# Install Tensorflow
---
## I don't have a Nvidia nuclear bomb so I will install a CPU only Tensorflow :)
There are 4 mechanisms to install Tensorflow, Google recommends to install with virtualenv.

Take the following steps to install TensorFlow with Virtualenv:

1. Install pip and virtualenv by issuing the following command:
```
$ sudo apt-get install python-pip python-dev python-virtualenv
```
2. Create a virtualenv environment by issuing the following command:
```
$ virtualenv --system-site-packages [targetDirectory]
```
The [targetDirectory] specifies the top of the virtualenv tree. Our instructions assume that targetDirectory is ~/tensorflow, but you may choose any directory.

3. Activate the virtualenv environment by issuing one of the following commands:
```
$ source ~/tensorflow/bin/activate # bash, sh, ksh, or zsh
$ source ~/tensorflow/bin/activate.csh  # csh or tcsh
```
The preceding source command should change your prompt to the following:
```
 (tensorflow)$
```
4. Issue one of the following commands to install TensorFlow in the active virtualenv environment:
```
(tensorflow)$ pip install --upgrade tensorflow      # for Python 2.7
(tensorflow)$ pip3 install --upgrade tensorflow     # for Python 3.n
(tensorflow)$ pip install --upgrade tensorflow-gpu  # for Python 2.7 and GPU
(tensorflow)$ pip3 install --upgrade tensorflow-gpu # for Python 3.n and GPU
```
If the preceding command succeeds, skip Step 5. If the preceding command fails, perform Step 5.

5. (Optional) If Step 4 failed (typically because you invoked a pip version lower than 8.1), install TensorFlow in the active virtualenv environment by issuing a command of the following format:
```
(tensorflow)$ pip install --upgrade TF_PYTHON_URL   # Python 2.7
(tensorflow)$ pip3 install --upgrade TF_PYTHON_URL  # Python 3.N
```
where TF_PYTHON_URL identifies the URL of the TensorFlow Python package. The appropriate value of TF_PYTHON_URLdepends on the operating system, Python version, and GPU support. Find the appropriate value for TF_PYTHON_URL for your system [here](https://www.tensorflow.org/install/install_linux#the_url_of_the_tensorflow_python_package). For example, if you are installing TensorFlow for Linux, Python 2.7, and CPU-only support, issue the following command to install TensorFlow in the active virtualenv environment:
```
(tensorflow)$ pip3 install --upgrade \
 https://storage.googleapis.com/tensorflow/linux/cpu/tensorflow-1.1.0-cp34-cp34m-linux_x86_64.whl
```
 ### Next Steps
After installing TensorFlow, [validate the installation](https://www.tensorflow.org/install/install_linux#ValidateYourInstallation).
Note that you must activate the virtualenv environment each time you use TensorFlow. If the virtualenv environment is not currently active, invoke one of the following commands:
```
$ source ~/tensorflow/bin/activate      # bash, sh, ksh, or zsh
$ source ~/tensorflow/bin/activate.csh  # csh or tcsh
```
When the virtualenv environment is active, you may run TensorFlow programs from this shell. Your prompt will become the following to indicate that your tensorflow environment is active:
```
(tensorflow)$
```
When you are done using TensorFlow, you may deactivate the environment by invoking the deactivate function as follows:
```
(tensorflow)$ deactivate
```
The prompt will revert back to your default prompt (as defined by the PS1 environment variable).
### Uninstalling TensorFlow
To uninstall TensorFlow, simply remove the tree you created. For example:
```
$ rm -r [targetDirectory]
```
# Install Caffe
---
## Same as TensorFlow, install CPU only mode.
### General dependencies
```
sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler
sudo apt-get install --no-install-recommends libboost-all-dev
```
For better CPU performance, install ATLAS by:
```
sudo apt-get install libatlas-base-dev
```
**The default language of Caffe is C++, if you want to use Python(like me:) ), you will need to install python-dev or python3-dev.**

**The official document says these dependencies need to be installed only in Ubuntu 14.04. BUT in Ubuntu 17.04 I'm using it seems that you have to install these packages,too:**
```
sudo apt-get install libgflags-dev libgoogle-glog-dev liblmdb-dev
```
### Compilation
Caffe can be compiled with either Make or CMake. Make is officially supported while CMake is supported by the community.

#### Compile with Make
Configure the build by copying and modifying the example Makefile.config for your setup. The defaults should work, but uncomment the relevant lines if using Anaconda Python.
```
git clone https://github.com/BVLC/caffe.git
cd caffe
cp Makefile.config.example Makefile.config
# Adjust Makefile.config (for example, if using Anaconda Python, or if cuDNN is desired)
make all
make test
make runtest
make pycaffe
make distribute
```
I plan to use Python3 in CPU mode, so edit the Makefile.config as this:
+ (Uncomment) CPU_ONLY := 1
+ (Uncomment & Edit) PYTHON_LIBRARIES := boost_python-py35 python3.5m
+ (Uncomment & Edit) PYTHON_INCLUDE := /usr/include/python3.5m /usr/lib/python3/dist-packages/numpy/core/include
+ (Edit) INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial/
+ (Edit)LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib /usr/lib/x86_64-linux-gnu/hdf5/serial/

**Speed**: for a faster build, compile in parallel by doing make all -j8 where 8 is the number of parallel threads for compilation (a good choice for the number of threads is the number of cores in your machine).

#### Set PYTHONPATH
Open your .bashrc or other .xxxshrc file and add this:
```
# PythonPath to caffe
  export PYTHONPATH=[your path to caffe]/python:$PYTHONPATH
```
#### Test the installation
Open a Python3 IDLE and try to import caffe, if no error occurred......

```
➜  caffe git:(master) python3
Python 3.5.3 (default, Jan 19 2017, 14:11:04)
[GCC 6.3.0 20170118] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import caffe
>>>
```
*Congratulations! Enjoy it!*

If python3 said can not found skimage, do this:
```
sudo apt install python3-skimage
```
