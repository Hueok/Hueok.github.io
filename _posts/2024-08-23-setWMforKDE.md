---
title: "Arch Linux - How to Use Other WM for KDE"
categories: [Others]
tags: [linux, wm] # TAG names should always be lowercase
---

> KDE Plasma 6 DE를 기준으로 기본 WM인 Kwin 말고 다른 WM을 사용하는 방법에 대해 정리한다.

Plasma 5.25 이후로 기존의 Xsession을 이용하는 방법 말고, systemd의 service파일을 만들어서 mask/unmask를 통해서도 WM 설정이 가능하다.

그러나 기존의 Xsession을 이용하는 방법이 나에겐 더 편해서 Xsession만 정리한다.


## Create New Xsession
기본적으로 `.desktop`파일들은 `/usr/share/xsessions/`에 위치한다. 여기에 임의 `.desktop`파일을 생성하자. 

예시로, `bspwm`을 사용했으므로 `plasma-bspwm.desktop`를 만들어줬다.

아래와 같은 내용을 가진다.

```
[Desktop Entry]
Type=XSession
Exec=env KDEWM=/usr/bin/bspwm /usr/bin/startplasma-x11
TryExec=/usr/bin/startplasma-x11
DesktopNames=KDE
Name=Plasma bspwm
```
이처럼, `KDEWM=`요소에 원하는 WM의 주소를 입력하는것이 주요 아이디어다.

## Disable Systemd
Plasma 5.25 이후 버전을 사용하고 있다면 KDE가 시스템 부트 시 systemd를 사용하지 않도록 해줘야한다.
```shell
$ kwriteconfig5 --file startkderc --group General --key systemdBoot false
```

## Select session in Login Manager
SDDM과같은 Login Manager에서 방금 만든 desktop session을 선택하고 로그인하면 끗.
