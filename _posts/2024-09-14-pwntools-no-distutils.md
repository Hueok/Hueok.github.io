---
title: "pwntools : Could not populate PLT: No module named 'distutils'"
categories: [Others]
tags: [pwntools] # TAG names should always be lowercase
---

> pwntools 사용 중 [!] Could not populate PLT: No module named 'distutils' 가 나타났을때..

```shell
$ apt install python3-distutils
$ pip install setuptools # This solved my problem
```
