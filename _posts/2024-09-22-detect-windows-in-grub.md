---
title: "Add Windows in Grub in Arch"
categories: [Others]
tags: [arch] # TAG names should always be lowercase
---

> Windows + Arch Linux 듀얼부팅 환경을 구축하고자 할 때, Arch의 Grub에 windows를 추가하는 방법에 대해 정리한다.

## Procedure

1. Install required packages `os-prober`, `ntfs-3g`

```shell
$ sudo pacman -S os-prober ntfs-3g
```

2. Edit `/etc/default/grub`.

    - Uncomment `GRUB_DISABLE_OS_PROBER=false` at the bottom of the file.

3. Update grub config

```shell
$ sudo grub-mkconfig -o /boot/grub/grub.cfg
```

