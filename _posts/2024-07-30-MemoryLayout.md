---
title: "SystemHacking-Memory Layout"
categories: [Security, SystemHacking]
tags: [memory] # TAG names should always be lowercase
---

# What is Memory layout?
매우 오래전에는 모든 데이터들(Function, data, lib)을 하나의 페이지 내에 저장해서 사용하는 방식을 사용했으나, 이는 매우 취약한 형태이므로 오늘날은 발전된 Memory Layout을 사용한다

> 예를들어, malicious 데이터를 data에 입력해두고, Function을 포인팅하는 레지스터가 malicious데이터를 지칭하도록 만들면 바로 실행되는 등의 취약점이 존재함.
{: .prompt-tip}

![img](/images/MemoryLayout_img/memoryLayout.png)
_Linux User Application Memory Structure_


<br/>
크게 text, data, heap, lib, stack 영역으로 나뉘어는데, 간단한 예제를 통해 알아보자.

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

Memory Layout이 어떻게 구성되어 있는지에 따라 exploit할 수 있는 방법이 달라지기 때문에 이를 이해하는것은 중요하다.
> - 어디에 어떤 코드들과 데이터들이 있는지
> - 영역의 권한은 어떠한지
{: .prompt-warning}