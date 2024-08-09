---
title: "Computer Science - Calling Convention"
categories: [CS, Linux]
tags: [calling convention, cdecl, sysv] # TAG names should always be lowercase
---

# 함수 호출 규약?

함수 호출 규약은 함수의 호출 및 반환에 관한 규약이다. 함수를 호출할 때 호출자의 `Stack Frame`, `Return Address`를 저장해야하고, 피호출자가 요구하는 *인자*를 전달해야한다. 또한 함수가 반환될 때, 피호출자의 반환값을 호출자가 전달 받아야한다.

Calling Convention을 적용하는 것은 **컴파일러**가 한다. 같은 호출 규약 이라 할지라도 컴파일러마다 다르게 구현할 수도 있다. 프로그래머가 따로 명시하지 않으면 컴파일러는 아키텍처에 따라 그에 적합한 호출규약을 자동으로 적용한다. 

윈도우는 *MSVC*, 리눅스는 *gcc* 컴파일러를 주로 사용한다. 같은 x86-64 아키텍처일때, 각각 *MSVC*는 *MS x64*, *gcc*는 *SYSTEM V* 호출 규약을 사용한다.

```
x86 
  - cdecl << 
  - stdcall
  - fastcall
  - thiscall

x86-64
  - System V AMD64 ABI의 Calling Convention <<
  - MS ABI의 Calling Convention
```

# x86-64호출 규약: SYSV
리눅스는 SYSV ABI (SYSTEM V Application Binary Interface)를 기반으로 만들어졌다. SYSV ABI는 ELF포맷, 링킹 방법, 함수 호출 규약등의 내용을 담고있다.

SYSV의 함수 호출 규약은 다음 성격을 갖는다.
```
1. 6개의 인자를 RDI, RSI, RDX, RCX, R8, R9에 순서대로 저장하여 전달한다. 더 많은 인자를 사용해야 할 때는 스택을 이용한다.
2. Caller에서 인자 전달에 사용된 스택을 정리한다.
3. 함수의 반환 값은 RAX로 전달한다.
```

# x86호출 규약: cdecl
x86아키텍처는 레지스터의 수가 적다. 

cdecl은 다음 성격을 갖는다.
```
1. 스택만을 통해 인자를 전달한다.
2. 인자를 전달할 때 사용한 스택을 호출자가 정리한다.
3. 인자 전달 시, 마지막 인자부터 첫번째 인자 순서로 스택에 push한다.
```

## 함수의 인자 접근 방법
```
arg1 : ebp + 8
arg2 : ebp + 12
arg3 : ebp + 16
```
위와 같이, 호출된 함수의 `ebp`를 기준으로 하위 `SFP` + `RET` 총 8바이트 이후부터 4바이트 단위로 인자를 취급한다.

단, 최적화 컴파일한 바이너리는 인자 접근에 다른 방법을 사용한다. `esp`를 사용하는데, 함수의 인자와 지역변수 모두 esp로 접근하므로 스택프레임 접근 및 레지스터 사용을 최소화하는 방법이다. 기본적으로 아래와 같은 형식을 따른다.
```
arg1 : esp + 4
arg2 : esp + 8
arg3 : esp + 12
```

### Optimization options for Compiler

| 최적화 옵션 | 의미 |
|-------------|-----------------------------|
| -O0(기본값) | 최적화 X |
| -O          | 코드 크기와 실행 시간을 줄임 |
| -O1         | -O |
| -O2         | 메모리 공간과 속도를 희생하지 않는 범위 내의 모든 최적화를 수행. `loop unrolling`과 `function inlining`에 대한 최적화는 수행하지 않음. |
| -O3         | -O2 + 인라인 함수와 레지스터에 대한 최적화를 추가로 수행 |
| -Os         | -O2 - 코드 크기를 증가시키는 최적화는 생략 |

