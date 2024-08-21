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

그리고 x64환경에서 `printf`함수는 `rdi`에 FormatString을, `rsi`부터 스택에 FormatString의 인자를 전달한다.

```text
Format String Bug based on cdecl calling convention
+-------------------+
|        rdi        | << Format String 
+-------------------+
|        rsi        | << %1$
+-------------------+
|        rdx        | << %2$
+-------------------+
|        rcx        | << %3$
+-------------------+
|        r8         | << %4$
+-------------------+
|        r9         | << %5$
+-------------------+
|    Stack Data1    | << %6$
+-------------------+
|    Stack Data2    | << %7$
+-------------------+
|    Stack Data3    | << %8$
+-------------------+
|    Stack Data4    | << %9$
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
