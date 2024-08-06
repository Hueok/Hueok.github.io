---
title: "System Hacking - Return To Library"
categories: [Security, SystemHacking]
tags: [rtl, pwnable, rop] # TAG names should always be lowercase
---

> NX를 우회하는 기법인 Return To Library의 기본적인 동작과 ROP의 기본 개념에 대해서 공부한 기록을 작성한다.

## What RTL?
NX가 적용되면 스택이나 버퍼에 Shellcode를 올려서 공격하는 *Return To Shellcode*공격이 어렵다. 이는 실행 권한 때문인데, 실행 권한이 살아있는 메모리 영역에서 공격이 가행된다면 문제없이 공격이 가능할 것이다. 이 점을 이해하고, `BOF`로 *Return Address*를 실행 권한이 남아있는 코드 영역으로 덮어쓰는 방향성을 잡을 수 있다.

일반적으로, NX가 적용되어 있음에도 실행 권한이 살아있는 메모리 영역은 **바이너리의 코드 영역**과 **라이브러리의 코드 영역**이다. 이중 라이브러리 코드 영역에는 다양한 함수가 구현되어 있는데, 공격자가 사용할 법한 함수들도 구현이 되어있기 때문에 주목해 볼만 하다.

리눅스에서 C언어로 작성된 프로그램이 참조하는 `libc`에는 `system`, `execve`등 프로세스 실행과 관련된 함수들이 구현되어 있다. 이를 이용해서 NX를 우회하고 셸을 획득하는 공격을 `Return To Libc`라고 부른다. `libc`에 한정되지 않고, 다른 라이브러리도 활용될 수 있으므로 `Return To Library`라고도 불린다.

이 하위 공격 분류로, `Return To PLT`가 있는데, 근본적으로 라이브러리의 코드를 사용하는 것이 핵심이므로 RTL을 이해하고 나면, 이것의 응용 방안정도로 생각할 수 있겠다.


## ROP

### Return Gadget
리턴 가젯은 `ret`명령어로 끝나는 어셈블리 코드조각을 의미한다. `ROPgadget`명령어로 리턴 가젯을 찾을 수 있다.
```shell
$ ROPgadget --binary {process} 
# --re {regexp} 옵션 추가 시 해당 정규표현식을 갖는 리턴가젯만 찾음
```

### Example
얘를들어, Return Address와 그로부터의 하위 N바이트의 스택에 다음과같은 가젯을 삽입하면, `system("/bin/sh")`를 실행할 수 있는것이다.
```text
addr of ("pop rdi; ret")   <= return address
addr of string "/bin/sh"   <= ret + 0x8
addr of "system" plt       <= ret + 0x10
```

RTL공격은 **Return Oriented Programming**으로 발전한다. 바이너리의 가젯과 라이브러리의 가젯을 이용해서 어셈블리어 프로그래밍 하듯이 복잡한 실행 흐름을 구현하는 기법을 `ROP`라고 부르는데, 상황에 따라 `ROP`를 이용해서 RTL, GOT overwrite 등의 페이로드를 작성할 수 있는것이다.