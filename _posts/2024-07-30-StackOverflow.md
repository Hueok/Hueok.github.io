---
title: "System Hacking - Stack Overflow + DEP"
categories: [Security, SystemHacking]
tags: [stack overflow, dep] # TAG names should always be lowercase
---

# Stack ?
스택에는 아래와 같은것들이 저장된다.
- __Local Varaible__
- __Return Address__
- __Base Pointer__
- __Argument__

`Stack`에는 영역이 존재한다. 이 영역을 구분하기 위해 존재하는것이 `Stack Frame`이다.

`Stack Frame`은 함수가 호출될 때, 해당 함수만의 Stack 영역을 구분하기 위해 생성되는 공간이다. 이 공간에는 해당 함수와 관련있는 지역변수, 매개변수, 리턴주소가 함수 호출 시에 할당되어 저장되며 함수가 종료될때 소멸한다.

![img](/images/StackOverflow_img/StackFrame.png)
__일반적인 Stack Frame 구성__


> __Calling Convention__
> x32 Architecture에서는 Argument를 Stack에 저장한다.
> x62 Architecture에서는 첫번째 인자에 대하여 `rdi`, 두번째 인자에 대하여 `rsi`를 사용하여 저장한다. 인자를 저장할 레지스터가 남아있지 않아졌을 때 Stack에 인자를 저장한다.
{: .prompt-warning}

# Stack Overflow
Stack에서 일어나는 overflow현상을 의미한다.
발생하는 이유는 매우 많지만, 가장 대표적인 얘를 들어서 이해해보자.

```c
...
char buff[10];
scanf("%s", &buff);
...
```
`scanf()`의 `%s`로 인자를 받을때, 입력 크기 제한이 없다. 개발자는 변수 buff의 size를 10byte로 정의했지만, 실제 사용자의 입력은 이를 초과하는 크기를 가질 수 있다.

즉, 변수가 갖는 stack 범위를 초과해서 다른 영역까지 침범할 수 있다. 여기서 Stack의 `Return Address`에 새로운 값이 덮어 쓰여진다면 매우 위험하다.

공격자가 값을 입력해서, `eip` 레지스터를 통제할 수 있게된다. 예를들어, 공격자가 stack에 malicious function, shell code등을 삽입해두고 `Return Address`를 이 주소로 덮어 `eip` 레지스터가 이를 지칭하게 만들면~ 다 털리는겁니다.

## ret2stack test
스택 위치를 알기 위해 ASLR을 끄고 테스트해보자.
```shell
# Turn off ASLR 
$ echo "0" | sudo tee /proc/sys/kernel/randomize_va_space
```
```c
//poc.c
#include<stdio.h>
void vuln() {
    char buf[64];
    scanf("%s", buf);
}
int main() {
    vuln();
}
```
이제 이 프로그램에 대하여 Stack Overflow를 실습해보고 이해해보자.


```shell
# compile command
gcc -o poc poc.c -fno-stack-protector -no-pie -z relro -m32
```


gdb를 이용해 `vuln()`함수를 disassemble 하여 버퍼가 어디 저장되어있고, Return Address까지 접근하기 위해 몇바이트의 입력이 필요한지를 알아보자.

```text
(gdb) disas vuln
Dump of assembler code for function vuln:
   0x08049166 <+0>:     push   %ebp
   0x08049167 <+1>:     mov    %esp,%ebp
   0x08049169 <+3>:     push   %ebx
   0x0804916a <+4>:     sub    $0x44,%esp
   0x0804916d <+7>:     call   0x80491b1 <__x86.get_pc_thunk.ax>
   0x08049172 <+12>:    add    $0x2e82,%eax
   0x08049177 <+17>:    sub    $0x8,%esp
   0x0804917a <+20>:    lea    -0x48(%ebp),%edx
   0x0804917d <+23>:    push   %edx
   0x0804917e <+24>:    lea    -0x1fec(%eax),%edx
   0x08049184 <+30>:    push   %edx
   0x08049185 <+31>:    mov    %eax,%ebx
   0x08049187 <+33>:    call   0x8049040 <__isoc99_scanf@plt>
   0x0804918c <+38>:    add    $0x10,%esp
   0x0804918f <+41>:    nop
   0x08049190 <+42>:    mov    -0x4(%ebp),%ebx
   0x08049193 <+45>:    leave
   0x08049194 <+46>:    ret
End of assembler dump.
```
``` 0x0804917a <+20>:    lea    -0x48(%ebp),%edx```라인을 통해 `example.c`의 `buf`는 `ebp-0x48`에 위치함을 알 수 있다.

따라서 `ebp`까지의 거리는 72byte 이므로, `ebp`가 4byte 차지하므로 총 76byte를 입력한 후에는 `Return Address`를 control하는 입력을 할 수 있다.

실제로 되는지 테스트해보자.
`AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABBBB` : A*76 + B*4
이를 프로그램에 입력해보면,
```shell
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABBBB

Program received signal SIGSEGV, Segmentation fault.
0x42424242 in ?? ()
```
`Return Address`가 "BBBB" 즉, `0x42424242`로 변경되어 Segmentation Fault가 발생하는것을 확인할 수 있다.


## DEP (Data Execution Prevention)
단순히, stack의 permission중 execution을 빼버리면 해결되는 취약점이다.

---
개선필요 포스팅