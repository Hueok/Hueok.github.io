---
title: "System Hacking - GOT overwrite"
categories: [Security, SystemHacking]
tags: [got, pwnable] # TAG names should always be lowercase
---

> GOT overwrite의 기본 개념에 대해 공부한다.


# GOT overwrite 개념 및 원리
plt&got를 공부하면 임의 함수의 최초 호출 이후의 호출부터는 GOT에 쓰여있는 실제 함수의 주소를 참조한다는 것을 알 수 있다.(Full-RELRO 미적용 기준)

그러나 결정적으로, got의 함수 주소를 참조할 때, 그 어떠한 검증도 거치지 않는다는 취약점이 존재한다. 즉, 임의 함수의 got엔트리에 대하여 다른 함수의 주소가 쓰여있다면 다른 함수가 실행된다는 의미다.

이 취약점을 이용하기 위해 특정 함수의 GOT엔트리에 다른 함수의 주소를 덮어 쓰는 공격 기법을 `GOT overwrite`라 한다.

# GOT overwrite exploit example
예를들어 `read`함수를 `system`함수로 got overwrite 하려면,
1. `read`함수의 got 주소를 구한다
2. 1번에서 구한 주소에서 라이브러리상의 `read` offset을 빼서 library_base 메모리 주소를 구한다.
3. library_base + `system` offset을 통해 실행중인 바이너리의 `system`함수 주소를 구한다
4. ROP를 통해 1번에서 구한 `read`got 주소에 `system`함수를 덮어 쓴다.
5. 이후 `read`호출 시에는 `system`이 대신 호출된다. 

이 모든 과정에서 함수 호출 시 인자만 잘 설정해주면 된다.