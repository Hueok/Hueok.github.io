---
title: "Baek Joon - s4_10828_implementStack"
categories: [PS]
tags: [] # TAG names should always be lowercase
---
## 문제 :
> #### stack을 구현하는 문제
> #### 단순 구현 문제라 새롭게 얻어가는 부분은 적은듯...

## 풀이1 : #simply_use_STL
> c++ 에서는 STL에서 std::stack 컨테이너를 제공한다. 이는 너무 간단하니 STL을 사용하더라도 deque를 이용하여 구현하는 방법이다.

### idea :
> 단순히 STL을 사용한 케이스 분류

## Code 1:
```cpp
#include <iostream>
#include <deque>

int main(){
    int N;
    std::cin >> N;
    std::deque<int> stack;

    std::string command;
    int arg;
    for(int i=0; i<N; i++){
        std::cin >> command;
        if(command == "push"){
            std::cin >> arg;
            stack.push_back(arg);
        }
        else if(command == "pop"){
            if(stack.size() == 0){
                std::cout << -1 << std::endl;
            }
            else{
                std::cout << stack.back() << std::endl;
                stack.pop_back();
            }
        }
        else if(command == "size"){
            std::cout << stack.size() << std::endl;
        }
        else if(command == "empty"){
            int result = stack.empty()? 1 : 0;
            std::cout << result << std::endl;
        }
        else if(command == "top"){
            if(stack.size() == 0){
                std::cout << -1 << std::endl;
            }
            else{
                std::cout << stack.back() << std::endl;
            }
        }
    }
}
```
----------------------------------------------------------
## 풀이2 : #Full_implementatoin
> STL을 이용하지 않고 full 구현으로 커버치는 방법

### idea : 
> class의 member로 int* 형 메모리를 할당시켜주고 member function을 적절히 만들어 stack동작을 구현

## Code 2 :
```cpp
#include <iostream>
#include <string>

class Stack{
private:
    int* arr;
    int index;
public:
    Stack(int N){
        arr = new int[N];
        index = -1;
    }
    ~Stack(){
        delete[] arr;
    }

    void push(int num){
        arr[++index] = num;
    }
    int top(){
        if(index == -1) return -1;
        return arr[index];
    }
    int size(){
        return index+1;
    }
    int empty(){
        if(index==-1) return 1;
        return 0;
    }
    int pop(){
        if(index ==-1) return -1;
        return arr[index--];
    }
};

int main(){
    std::ios::sync_with_stdio(false);
    std::cin.tie(NULL); std::cout.tie(NULL);

    int N;
    std::string command;
    int arg;

    std::cin >> N;
    Stack stack(N);
    for(int i=0; i<N; i++){
        std::cin >> command;
        if(command == "push"){
            std::cin >> arg;
            stack.push(arg);
        }
        else if(command == "top") std::cout << stack.top() << std::endl;
        else if(command == "pop") std::cout << stack.pop() << std::endl;
        else if(command == "size") std::cout << stack.size() << std::endl;
        else if(command == "empty") std::cout << stack.empty() << std::endl;
    }
}
``
