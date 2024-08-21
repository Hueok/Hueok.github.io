---
title: "System Hacking - Format String Bug"
categories: [Security, SystemHacking]
tags: [pwnable, fsb, string] # TAG names should always be lowercase
---

> Explain main CONCEPT of FSB briefly...

# Format String Buf (FSB)
FomatString이 인자를 참조하는 특성에서 발생하는 버그다.

`cdecl` 함수 호출규약을 기준으로 생각해보자.

함수에 주어지는 인자는 그 순서에 맞게 `rdi`, `rsi`, `rdx`, `rcx`, `r8`, `r9` 에 저장되고, 그 이후는 스택에 저장된다고 공부했다.

FormatString의 `%[n]$`은 n번째 인자를 참조하는데, 이때 *함수에 주어진 인자의 개수를 검증하는 단계가 없다!*

따라서 아래와 같은 형태로 포맷스트링이 레지스터와 스택을 불러온다.

```text
Format String Bug based on cdecl calling convention
+-------------------+
|        rdi        | << %1$
+-------------------+
|        rsi        | << %2$
+-------------------+
|        rdx        | << %3$
+-------------------+
|        rcx        | << %4$
+-------------------+
|        r8         | << %5$
+-------------------+
|        r9         | << %6$
+-------------------+
|    Stack Data1    | << %7$
+-------------------+
|    Stack Data2    | << %8$
+-------------------+
|    Stack Data3    | << %9$
+-------------------+
|    Stack Data4    | << %10$
+-------------------+
|        ...        | << %[n]$
+-------------------+
```

## example
```python
# python using pwntools
# Assume addr_var is given
fstring = b'%100c%9$n'.ljust(16)
fstring += p64(addr_var)
p.sendline(fstring)
    # fstring will be handled in the form like as printf(fstring)
    # The result of this fstring : store 100 to var
```
