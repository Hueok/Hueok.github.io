---
title: "Arch Linux - Hyprsome Setup : when hyprland key binds not work"
categories: [Others]
tags: [linux, hyprland] # TAG names should always be lowercase
---

> Hyprland v0.42.0 에서 현 시간(2024-08-28) 기준 hyprpm을 이용한 split-monitor-workspaces가 config파일에서 적용 에러가 나는것을 확인했고, 이는 실제로 bug oppen상태로 분류되어 있었다.

> 그럼에도 workspaces per monitor 형태로 시스템을 구축하고 싶다면 이와 비슷한 목적을 가진 프로젝트인 `hyprsome`을 사용하자.

# Install Hyprsome
[Hyprsome Github](https://github.com/sopa0/hyprsome)

```shell
$ git clone https://github.com/sopa0/hyprsome.git
$ cargo install hyprsome
```
rust 기반 프로젝트 이므로 cargo로 설치해주면 된다.


```conf
# ~/.config/hypr/hyprland.conf
...
bind=SUPER,1,exec,hyprsome workspace 1
bind=SUPER,2,exec,hyprsome workspace 2
bind=SUPER,3,exec,hyprsome workspace 3
bind=SUPER,4,exec,hyprsome workspace 4
bind=SUPER,5,exec,hyprsome workspace 5

bind=SUPERSHIFT,1,exec,hyprsome move 1
bind=SUPERSHIFT,2,exec,hyprsome move 2
bind=SUPERSHIFT,3,exec,hyprsome move 3
bind=SUPERSHIFT,4,exec,hyprsome move 4
bind=SUPERSHIFT,5,exec,hyprsome move 5
...
```
`hyprland.conf`에 이와 같은 형식으로 사용할 수 있다. 또는 shell에서 아래 명령어로 도움말을 확인할 수도 있다.
```shell
$ hyprsome --help
```

---
# If key binds not working...

`hyprland.conf`에 키 바인딩을 올바르게 추가했음에도 커맨드가 동작하지 않는다면 아래와 같은 방향으로 시도해보자

### CLI환경에서 TEST

터미널을 열어서 다음 명령어를 통해 hyprsome이 올바르게 설치되어 있는지 확인해보자.
```shell
hyprsome workspace 2
hyprsome workspace 1
```

만약 터미널 환경에서는 이 명령어가 잘 적용된다면 이는 hyprland가 참조하는 PATH에 hyprsome 바이너리가 없다는 것이므로 따로 추가해주자.

쉽게 정리해서 깔끔하게 스탭을 정리해보면

```shell
$ git clone https://github.com/sopa0/hyprsome.git
$ cd hyprsome
$ sudo cp target/debug/hyprsome /usr/local/bin
```

이처럼, build된 바이너리를 `/usr/local/bin`위치에 직접 추가함으로써 hyprland에서 잘 작동할 것이다.
