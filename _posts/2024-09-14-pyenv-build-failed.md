---
title: "pyenv install : BUILD FAILED (Ubuntu 22.04 using python-build 20180424)"
categories: [Others]
tags: [pyenv, bug] # TAG names should always be lowercase
---

> pyenv install 3.x.x 시에 `BUILD FAILED (Ubuntu 22.04 using python-build 20180424)` 오류가 발생했을 때 해결 방법을 정리한다.


일반적으로 python 빌드를 위한 dependencies가 충족되어 있지 않았을 경우 발생한다. 

```shell
sudo apt update
sudo apt install \
    build-essential \
    curl \
    libbz2-dev \
    libffi-dev \
    liblzma-dev \
    libncursesw5-dev \
    libreadline-dev \
    libsqlite3-dev \
    libssl-dev \
    libxml2-dev \
    libxmlsec1-dev \
    llvm \
    make \
    tk-dev \
    wget \
    xz-utils \
    zlib1g-dev
```
