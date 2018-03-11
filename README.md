## Python 3.6

首先安装 Python 3.6，这里使用 Anaconda 来安装，下载地址：[https://www.anaconda.com/download/#linux](https://www.anaconda.com/download/#linux)，点击 Download 按钮下载即可，这里下载的是 Anaconda 3-5.1 版本。

下载下来之后目录下会出现一个 Anaconda3-5.1.0-Linux-x86_64.sh 文件，然后直接执行即可安装：

bash Anaconda3-5.1.0-Linux-x86_64.sh
执行完毕之后按照默认设置走下来即可完成安装。

CUDA 9.1
如果存在之前的旧版本，可以选择先卸载，以免和新的 CUDA 版本产生冲突，在 /usr/local/cuda/bin 目录下有一个 uninstall_cuda_*.pl 文件，可以直接运行卸载，命令如下：

sudo ./uninstall_cuda_*.pl
这样即可将 CUDA 全部卸载。

首先查看一下自己的电脑需要怎样的驱动，我们可以先到 http://www.nvidia.com/Download/index.aspx 查询下我们需要的是怎样的驱动，这里我的显卡是 GTX 1080，所以以此为例说明，勾选好对应的配置，点击 Search，可以看到查询结果如下所示：

Version:	390.25
Release Date:	2018.1.29
Operating System:	Linux 64-bit
Language:	English (US)
File Size:	77.48 MB
这里说明我们需要的版本是 390.25。

接下来我们再下载 CUDA 9.1，下载地址是：https://developer.nvidia.com/cuda-downloads，然后依次勾选好系统的版本，如图所示：
