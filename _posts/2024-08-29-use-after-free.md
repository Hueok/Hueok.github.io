---
title: "System Hacking - Use After Free Concept&Example"
categories: [Security, SystemHacking]
tags: [pwnable, uaf, memory] # TAG names should always be lowercase
---

> Use-After-Free 취약점의 기본 컨셉과 이를 활용한 예제를 정리한다.

> ** `ptmalloc2` Allocator 기준으로 정리했다.


# What is UAF?
해제된 메모리에 접근할 수 있을때 발생하는 취약점

1. 임의 메모리를 참조하는 포인터를 메모리 해제 후에 초기화하지 않거나, (Dangling Pointer)

2. 해제한 메모리를 초기화 하지 않고 다음 청크에 재할당 한다면 (Exploit Allocator Feature)

Use-After-Free 취약점이 발생할 수 있다. 익스플로잇 성공률도 다른것에 비해 높고, 현재까지도 자주 발견되는 취약점이다.

## Dangling Pointer
> Double Free Bug 파트로 이동

## Exploit Allocator Feature

`malloc`, `free`함수는 대상 메모리의 데이터를 초기화하지 않는다. 따라서 새롭게 할당한 청크가 이전에 임의 용도로 할당되어 사용되다가 해제된 청크를 재할당 한것이라면, 그때 썼던 데이터가 새로운 청크에 남아있다. 

```text
# Scenario
1. first = malloc(0x100)
(first에 데이터 쓰기)
...
2. free(first)
3. second = malloc(0x100) // 1번의 청크를 재할당
4. second에는 first에서 썼던 데이터가 남아있음
```

### Usage
적절히 초기화 되지 않은 메모리 값일 읽거나, 새로운 객체가 악의적인 값을 사용하도록 유도할 수 있다.

# Example
[uaf_overwrite write-up](https://hueok.github.io/posts/uaf-overwrite) 에서 정리
