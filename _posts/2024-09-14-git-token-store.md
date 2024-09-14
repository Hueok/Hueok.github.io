---
title: "Git - Github Personal Access Token Local에 저장하기"
categories: [Git]
tags: [git] # TAG names should always be lowercase
---

# Git credential

credential을 이용하면 Token을 Local에 저장하여 매번 입력하지 않고도 git을 사용할 수 있다.

```shell
$ git config --global credential.helper store

$ git config --global --list 

user.email=xxxxxxxxxxx@xxxxxxx.xxx
user.name=Hueok
credential.helper=store
```
