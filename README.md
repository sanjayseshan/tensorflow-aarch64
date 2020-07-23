# Tensorflow-aarch64

Tensorflow builds for 64bit ARM. See releases tab.

Built on Debian 10.4 Buster ARM64 port with Python 3.7.3. Known to work on Debian arm64 install, including Android Userland app. Should work on 64bit Raspberry Pi OS (buster) builds also.

Install using pip.

~~~
$ sudo pip3 install tensorflow-2.4.0-cp37-cp37m-linux_aarch64.whl
~~~


# To compile yourself (on Debian buster arm64 host):

1) Compile bazel

~~~
$ sudo apt-get install git wget vim zip build-essential python python3 python3-dev python3-pip libhdf5-dev openjdk-11-{jdk,jre}
$ cd ~
$ mkdir bazel
$ cd bazel
$ wget https://github.com/bazelbuild/bazel/releases/download/3.4.1/bazel-3.4.1-dist.zip
$ unzip bazel-3.4.1-dist.zip
$ ./compile.sh
~~~

2) Install bazel
~~~
$ sudo cp ~/bazel/output/bazel /usr/local/bin/
~~~

3) Install tensorflow dependencies
~~~
$ cd ~
$ git clone https://github.com/tensorflow/tensorflow.git
$ cd tensorflow
$ sudo pip3 install six wheel
$ sudo apt-get install python3-numpy python3-keras python3-keras-applications python3-preprocessing
~~~
4) Configure tensorflow with default options (no accelerations)
~~~
$ ./configure

You have bazel 3.4.1- (@non-git) installed.
Please specify the location of python. [Default is /usr/bin/python3]: 


Found possible Python library paths:
  /usr/local/lib/python3.7/dist-packages
  /usr/lib/python3/dist-packages
Please input the desired Python library path to use.  Default is [/usr/local/lib/python3.7/dist-packages]

Do you wish to build TensorFlow with OpenCL SYCL support? [y/N]: N
No OpenCL SYCL support will be enabled for TensorFlow.

Do you wish to build TensorFlow with ROCm support? [y/N]: N
No ROCm support will be enabled for TensorFlow.

Do you wish to build TensorFlow with CUDA support? [y/N]: N
No CUDA support will be enabled for TensorFlow.

Do you wish to download a fresh release of clang? (Experimental) [y/N]: N
Clang will not be downloaded.

Please specify optimization flags to use during compilation when bazel option "--config=opt" is specified [Default is -march=native -Wno-sign-compare]: 


Would you like to interactively configure ./WORKSPACE for Android builds? [y/N]: N
Not configuring the WORKSPACE for Android builds.

Preconfigured Bazel build configs. You can use any of the below by adding "--config=<>" to your build command. See .bazelrc for more details.
	--config=mkl         	# Build with MKL support.
	--config=monolithic  	# Config for mostly static monolithic build.
	--config=ngraph      	# Build with Intel nGraph support.
	--config=numa        	# Build with NUMA support.
	--config=dynamic_kernels	# (Experimental) Build kernels into separate shared objects.
	--config=v2          	# Build TensorFlow 2.x instead of 1.x.
Preconfigured Bazel build configs to DISABLE default on features:
	--config=noaws       	# Disable AWS S3 filesystem support.
	--config=nogcp       	# Disable GCP support.
	--config=nohdfs      	# Disable HDFS support.
	--config=nonccl      	# Disable NVIDIA NCCL support.
Configuration finished

~~~
5) Compile tensorflow
~~~
$ bazel build --config=opt //tensorflow/tools/pip_package:build_pip_package --jobs 4
$ sudo mkdir /tfpkg
$ bazel-bin/tensorflow/tools/pip_package/build_pip_package /tfpkg/tensorflow_pkg
~~~
6) Install tensorflow
~~~
$ sudo pip3 install /tfpkg/tensorflow_pkg/tensorflow-*.whl
~~~
7) Test tensorflow
~~~
$ python3
Python 3.7.3 (default, Dec 20 2019, 18:57:59) 
[GCC 8.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import tensorflow
>>>
~~~
If there are no error messages, then you are done. Note that this may take several days to finish depending on your setup. On a QEMU aarch64 system on an Intel i7, this took roughly 7 days. Look at https://wiki.debian.org/Arm64Qemu to make a aarch64 VM. Cross compiling is probably faster than this.
