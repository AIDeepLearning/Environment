# TensorFlow GPU环境配置

本节详细说明一下深度学习环境配置，Ubuntu 16.04 + Nvidia GTX 1080 + Python 3.6 + CUDA 9.0 + cuDNN 7.1 + TensorFlow 1.6。

## Python 3.6

首先安装 Python 3.6，这里使用 Anaconda 3 来安装，下载地址：[https://www.anaconda.com/download/#linux](https://www.anaconda.com/download/#linux)，点击 Download 按钮下载即可，这里下载的是 Anaconda 3-5.1 版本，如果下载速度过慢可以选择使用清华镜像：[https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/](https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/)。

下载下来之后目录下会出现一个 Anaconda3-5.1.0-Linux-x86_64.sh 文件，然后直接执行即可安装：

```
bash Anaconda3-5.1.0-Linux-x86_64.sh
```

执行完毕之后按照默认设置走下来即可完成安装。

这里默认它会安装到用户目录下，如果想全局安装，可以在这一步输入你要安装的地址：

```
Anaconda3 will now be installed into this location:
/home/cqc/anaconda3

  - Press ENTER to confirm the location
  - Press CTRL-C to abort the installation
  - Or specify a different location below

[/home/cqc/anaconda3] >>> /usr/local/anaconda3
PREFIX=/usr/local/anaconda3
```

这里我指定了将其安装到 /usr/local/anaconda3 目录下，全局安装，所有用户共享，当然如果只想本用户使用的话使用默认配置即可。

安装完成之后添加 python3 和 pip3 的软链接：

```
sudo ln -s /usr/local/anaconda3/bin/python3 /usr/local/sbin/python3
sudo ln -s /usr/local/anaconda3/bin/pip /usr/local/sbin/pip3
```

这里是将软连接其添加到 /usr/local/sbin 目录下了，它默认会存在于环境变量中，因此可以直接调用。

当然也可以选择把 /usr/local/anaconda3/bin 目录添加到环境变量中，可以修改 ~/.bashrc 文件，添加如下内容：

```
export PATH=/usr/local/anaconda3/bin${PATH:+:${PATH}}
```

然后执行：

```
source ~/.bashrc
```

即可生效，下次登录时也会默认执行 ~/.bashrc 文件，也会生效。

接下来我们验证下 python3、pip3 命令是否都来自 Anaconda，命令如下：

```
pip3 -V
pip 9.0.1 from /usr/local/anaconda3/lib/python3.6/site-packages (python 3.6)
```

```
which python3
/usr/local/anaconda3/bin/python3
python3
Python 3.6.4 |Anaconda, Inc.| (default, Jan 16 2018, 18:10:19) 
[GCC 7.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> 
```

如果输入 pip3 和 python3 命令能出现如上类似结果，路径都在 /usr/local/anaconda3，就证明 Python 3 安装成功了。

## 安装驱动

首先查看一下自己的电脑需要怎样的驱动，我们可以先到 [http://www.nvidia.com/Download/index.aspx](http://www.nvidia.com/Download/index.aspx) 查询下我们需要的是怎样的驱动，这里我的显卡是 GTX 1080，所以以此为例说明，勾选好对应的配置：

![](https://germey.gitbooks.io/ai/content/assets/2018-03-11-18-46-41.jpg)

点击 Search，可以看到查询结果如下所示：

```
Version:	390.25
Release Date:	2018.1.29
Operating System:	Linux 64-bit
Language:	English (US)
File Size:	77.48 MB
```

这里说明我们需要的版本是 390.25。

接下来如果我们之前安装了驱动的话，可以重新安装一下，如果当前已经安装好了就不必了。

如果要重装，需要首先卸载掉之前的显卡驱动：

```
sudo apt-get remove –purge nvidia*
```

运行之后 NVIDIA 的一些驱动就被卸载了。

这时候 `nvidia-smi` 等命令已经不能用了，这就证明显卡驱动已经被卸载了。

然后接下来添加一个 PPA 源，命令如下：

```
sudo add-apt-repository ppa:graphics-drivers/ppa
```

然后更新一下：

```
sudo apt-get update
```

随后重新安装显卡驱动：

```
sudo apt-get install nvidia-390
```

注意这里的 390 就是刚才我们查询出来的版本，以实际查询出来的版本为准。


## CUDA 9.0

如果存在之前的旧版本，可以选择先卸载，以免和新的 CUDA 版本产生冲突，在 /usr/local/cuda/bin 目录下有一个 uninstall_cuda_*.pl 文件，可以直接运行卸载，命令如下：

```
sudo ./uninstall_cuda_*.pl
```

这样即可将 CUDA 全部卸载。

接下来我们再下载 CUDA 9.0，注意 TensorFlow 1.5 和 1.6 版本依然只是兼容 CUDA 9.0，没有兼容 CUDA 9.1，所以不要下载 9.1，CUDA 9.0 的下载地址是：[https://developer.nvidia.com/cuda-90-download-archive](https://developer.nvidia.com/cuda-90-download-archive)，然后依次勾选好系统的版本，如图所示：

![](https://germey.gitbooks.io/ai/content/assets/2018-03-11-23-37-55.jpg)

这里我们选择 Linux-x86_64-Ubuntu-16.04-runfile 的配置，然后点击 Base Installer 部分的 Download 按钮，下载 CUDA 9.0 安装包。

对应的下载命令是：

```
wget https://developer.nvidia.com/compute/cuda/9.0/Prod/local_installers/cuda_9.0.176_384.81_linux-run
```

执行此命令，等待下载完成即可。

接下来执行安装，运行如下命令：

```
sudo bash cuda_9.0.176_384.81_linux-run
```

安装过程需要输入一些确认选项，过程如下：

```
Description

The NVIDIA CUDA Toolkit provides command-line and graphical
tools for building, debugging and optimizing the performance
Do you accept the previously read EULA?
accept/decline/quit: accept

Install NVIDIA Accelerated Graphics Driver for Linux-x86_64 384.81?
(y)es/(n)o/(q)uit: n

Install the CUDA 9.0 Toolkit?
(y)es/(n)o/(q)uit: y

Enter Toolkit Location
 [ default is /usr/local/cuda-9.0 ]: 

Do you want to install a symbolic link at /usr/local/cuda?
(y)es/(n)o/(q)uit: y

Install the CUDA 9.0 Samples?
(y)es/(n)o/(q)uit: y

Enter CUDA Samples Location
 [ default is /home/cqc ]: 

Installing the CUDA Toolkit in /usr/local/cuda-9.0 ...
```

最后如果出现这样的提示，就证明 CUDA 安装好了：

```
Driver:   Not Selected
Toolkit:  Installed in /usr/local/cuda-9.0
Samples:  Installed in /home/cqc, but missing recommended libraries

Please make sure that
 -   PATH includes /usr/local/cuda-9.0/bin
 -   LD_LIBRARY_PATH includes /usr/local/cuda-9.0/lib64, or, add /usr/local/cuda-9.0/lib64 to /etc/ld.so.conf and run ldconfig as root

To uninstall the CUDA Toolkit, run the uninstall script in /usr/local/cuda-9.0/bin

Please see CUDA_Installation_Guide_Linux.pdf in /usr/local/cuda-9.0/doc/pdf for detailed information on setting up CUDA.

***WARNING: Incomplete installation! This installation did not install the CUDA Driver. A driver of version at least 384.00 is required for CUDA 9.0 functionality to work.
To install the driver using this installer, run the following command, replacing <CudaInstaller> with the name of this run file:
    sudo <CudaInstaller>.run -silent -driver
```

然后我们需要配置一下环境变量，更改 ~/.bashrc 文件，添加如下几行：

```
export PATH=/usr/local/cuda/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
export CUDA_HOME=/usr/local/cuda
```

修改完毕之后执行一下使其生效：

```
source ~/.bashrc
```

这时我们输出 CUDA_HOME、LD_LIBRARY_PATH 就可以看到对应的输出了：

```
echo $CUDA_HOME
/usr/local/cuda
echo $LD_LIBRARY_PATH
/usr/local/cuda/lib64
```

这样就代表环境变量生效了，CUDA 安装完成。

## cuDNN 7.1

cuDNN 的全称是 The NVIDIA CUDA® Deep Neural Network library，是专门用来对深度学习加速的库，它支持 Caffe2, MATLAB, Microsoft Cognitive Toolkit, TensorFlow, Theano 及 PyTorch 等深度学习的加速优化，目前最新版本是 cuDNN 7.1，接下来我们来看下它的安装方式。

下载链接：[https://developer.nvidia.com/rdp/cudnn-download](https://developer.nvidia.com/rdp/cudnn-download)，需要注册之后才能打开，这里我们选择 cuDNN v7.1.1 (Feb 28, 2018), for CUDA 9.0，然后选择 cuDNN v7.1.1 Library for Linux，如图所示：

![](https://germey.gitbooks.io/ai/content/assets/2018-03-11-23-49-17.jpg)

下载下来之后解压安装即可：

```
tar -zxvf cudnn-9.0-linux-x64-v7.1.tgz
sudo cp cuda/include/cudnn.h /usr/local/cuda/include/
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64/ -d
sudo chmod a+r /usr/local/cuda/include/cudnn.h
sudo chmod a+r /usr/local/cuda/lib64/libcudnn*
```

执行完如上命令之后，cuDNN 就安装好了，这时我们可以发现在 /usr/local/cuda/include 目录下就多了 cudnn.h 头文件。

## TensorFlow 1.6

到现在为止 Python 3.6、CUDA 9.0 和 cuDNN 7.1 就已经安装好了，而且环境变量也配置好了，接下来我们直接安装 TensorFlow 1.6 即可，TensorFlow 1.6 版本针对 CUDA 9 和 cuDNN 7 做了优化，可以预构建二进制文件。

这里需要安装的是 TensorFlow 的 GPU 版本，命令如下：

```
pip3 install tensorflow-gpu==1.6.0
```

安装完成之后验证一下：

```python
import tensorflow
```

如果没有报错，那就证明全部环境配置都成功了。

以上便是 Ubuntu 16.04 + Nvidia GTX 1080 + Python 3.6 + CUDA 9.0 + cuDNN 7.1 + TensorFlow 1.6 完整环境配置过程。
