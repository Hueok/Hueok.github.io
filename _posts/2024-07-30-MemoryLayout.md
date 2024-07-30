---
title: "Computer Science - Memory Layout"
categories: [CS]
tags: [memory] # TAG names should always be lowercase
---

> Memory에 대해 공부한다. [DreamHack]내용을 따른다.

# What is Memory layout?
매우 오래전에는 모든 데이터들(Function, data, lib)을 하나의 페이지 내에 저장해서 사용하는 방식을 사용했으나, 이는 매우 취약한 형태이므로 오늘날은 발전된 Memory Layout을 사용한다

> 예를들어, malicious 데이터를 data에 입력해두고, Function을 포인팅하는 레지스터가 malicious데이터를 지칭하도록 만들면 바로 실행되는 등의 취약점이 존재함.
{: .prompt-tip}

![img](/images/MemoryLayout_img/mem.png)
_Linux Memory Structure_


<br/>
오늘날 Memory는 크게 __Text__,  __Data__, __BSS__, __Heap__, __Stack__ 영역으로 나뉘어는데, 각 영역에 용도에 맞는 권한을 부여할 수 있다. 간단한 예제를 통해 알아보자.

## Example of memory layout
```c
//memory.c
#include <stdio.h>
#include <unistd.h>

int uninitialized;
int initialized = 1;

int main()
{
        void *heap = malloc(1024);
        int stack = 1024;
        char buf[64];

        printf("Function : 0x%lX\n", &main);
        printf("Uninitialized Data : 0x%lX\n", &initialized);
        printf("Initialized Data : 0x%lX\n", &initialized);
        printf("Heap : 0x%lX\n", heap);
        printf("Stack : 0x%lX\n", &stack);
}
```
```shell
#output of memory.c
Function : 0x55BE60E09149
Uninitialized Data : 0x55BE60E0C020
Initialized Data : 0x55BE60E0C020
Heap : 0x55BE617E62A0
Stack : 0x7FFD5B88D524
```
각 데이터를 저장하는 address가 분리되어있음을 확인할 수 있다.

## Text Segment (Also called Code Segment)
 __실행 가능한 기계 코드__ 가 위치하는 영역이다. `r-x` 권한이 부여된다.

## Data Segment
 __컴파일 시점에 값이 정해진 Global Variable(constant)__가 위치하는 영역이다. 부여되는 권한에 따라 두가지 영역으로 나뉘어지는데, 두 영역 모두 기본적으로 읽기 권한이 부여된다.

 전역변수는 `rw-`권한이 부여된 _data segment_ 에 위치하고, 전역 상수는 `r--`권한이 부여된 _rodata segment_에 위치한다.

```c
//! example
int num = 10;                          // data
char str[] = "writable";                // data
const char pre_str[] = "non-writable";  // rodata
char *str_ptr = "non-writable";  // str_ptr은 data, 문자열은 rodata

int main() { ... }
```

## BSS Segment (Block Started By Symbol Segment)
 __컴파일 시점에 값이 정해지지 않은 전역 변수__가 위치하는 메모리 영역이다. `rw-`권한이 부여된다. 프로그램이 시작될 때, 모두 0으로 초기화된다.

```c
int bss_data; // its in BSS

int main() {
  printf("%d\n", bss_data);  // 0
  return 0;
}
```

## Stack Segment
 `Stack Frame`단위로 사용된다. 프로세스를 시작할 때는 작은 크기의 Stack Segment를 할당하고, 부족해 질 때마다 이를 낮은 주소로 확장한다. `rw-`권한이 부여된다. 

## Heap Segment
 스택과 마찬가지로 동적으로 할당 될 수 있으며, 리눅스에서는 스택 세그먼트와 반대 방향으로 자란다. 일반적으로 `rw-`권한이 부여된다.
 > Heap과 Stack의 확장 방향이 다른 이유는 충돌을 해결하기 위함이다. 스택 영역을 메모리 끝에 위치시키고 서로 다른 방향으로 자라게 함으로써 힙과 스택이 최대한 자유롭게 사용될 수 있게 한다.
 {: .prompt-warning}

## Summary

| 세그먼트     | 역할                         | 일반적인 권한  | 사용 예                     |
| ------------ | ---------------------------- | -------------- | --------------------------- |
| 코드 세그먼트 | 실행 가능한 코드가 저장된 영역 | 읽기, 실행     | `main()` 등의 함수 코드     |
| 데이터 세그먼트 | 초기화된 전역 변수 또는 상수가 위치하는 영역 | 읽기와 쓰기 또는 읽기 전용 | 초기화된 전역 변수, 전역 상수 |
| BSS 세그먼트  | 초기화되지 않은 데이터가 위치하는 영역 | 읽기, 쓰기     | 초기화되지 않은 전역 변수   |
| 스택 세그먼트  | 임시 변수가 저장되는 영역      | 읽기, 쓰기     | 지역 변수, 함수의 인자 등   |
| 힙 세그먼트   | 실행중에 동적으로 사용되는 영역 | 읽기, 쓰기     | `malloc()`, `calloc()` 등으로 할당 받은 메모리 |


Memory Layout이 어떻게 구성되어 있는지에 따라 exploit할 수 있는 방법이 달라지기 때문에 이를 이해하는것은 중요하다.
> - 어디에 어떤 코드들과 데이터들이 있는지
> - 영역의 권한은 어떠한지
{: .prompt-warning}