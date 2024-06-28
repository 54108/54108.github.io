---
layout: post
title: "Intel A770 GPU深度学习环境搭建（Linux）"
date:   2024-6-28
tags: [env setup]
comments: true
author: Pd.ch
---

###### 说明：Windows下也可以进行环境搭建，但是个人更偏爱Debian。

<!-- more -->

### 目录

- [Step 0. 准备工作](#step-0-准备工作)
- [Step 1. 安装GPU驱动](#step-1-安装GPU驱动)
- [Step 2. 安装Intel®-oneAPI-Base-Toolkit](#step-2-安装Intel®-oneAPI-Base-Toolkit)
- [Step 3. 安装Intel®-Extension-forPyTorch](#step-3-安装Intel®-Extension-for-PyTorch)
- [Step 4. 安装xpu-smi](#step-4-安装xpu-smi)

## Step 0. 准备工作

首先你需要有一台使用Intel GPU的电脑，本文针对Intel Arc A770（16GB）编写，系统环境为Debian sid。
关于Intel® Extension for PyTorch的安装可以参考官方示例<https://intel.github.io/intel-extension-for-pytorch/index.html#installation?platform=gpu&version=v2.1.30%2bxpu&os=linux%2fwsl2&package=pip>。以下部分是我自己踩的一点坑。

## step 1. 安装GPU驱动

前往Intel GPU驱动网站<https://dgpu-docs.intel.com/driver/client/overview.html>，进行其中的3.1.1-3.1.5部分。树外内核部分无需理会。

## step 2. 安装Intel®-oneAPI-Base-Toolkit

前往Intel®-oneAPI-Base-Toolkit网站<https://www.intel.com/content/www/us/en/developer/tools/oneapi/base-toolkit-download.html?operatingsystem=linux&linux-install-type=apt>，使用apt进行安装。

## step 3. 安装Intel®-Extension-forPyTorch

我选择的是使用pip进行安装。你可以先使用conda创建一个虚拟环境，python版本推荐3.11。
安装命令
~~~
python -m pip install torch==2.1.0.post2 torchvision==0.16.0.post2 torchaudio==2.1.0.post2 intel-extension-for-pytorch==2.1.30.post0 oneccl_bind_pt==2.1.300+xpu --extra-index-url https://pytorch-extension.intel.com/release-whl/stable/xpu/cn/
~~~
完成后进行测试
~~~
source /opt/intel/oneapi/setvars.sh
~~~
~~~
python -c "import torch; import intel_extension_for_pytorch as ipex; print(torch.__version__); print(ipex.__version__); [print(f'[{i}]: {torch.xpu.get_device_properties(i)}') for i in range(torch.xpu.device_count())];"
~~~
最后成功识别到显卡即为安装成功。
前往example进行愉快的玩耍吧<https://intel.github.io/intel-extension-for-pytorch/xpu/2.1.30+xpu/tutorials/examples.html>。

# step 4. 安装xpu-smi

最后一部分我也还在摸索。使用这个软件也是因为intel-gpu-tools查看不了显存使用情况。
前往release页面下载安装即可<https://github.com/intel/xpumanager/releases/tag/V1.2.37>。
使用说明在GitHub仓库的readme中。