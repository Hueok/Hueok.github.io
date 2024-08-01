---
title: "Baek Joon - s2_1874_stackSequence"
categories: [PS]
tags: [] # TAG names should always be lowercase
---
## 문제 : 
> #### 1부터 N까지의 수를 차례로 스택에 push||pop하여 주어진 stack_sequence를 만드는 문제

## 풀이 : #implementation
> #### 로직을 생각해내고 잘 구현하면 되는 문제

### idea : 
> #### stack.back()과 사용자 입력값의 대소비교에 따른 기능구현
> #### 지금 찾아야하는 값이 stack.back()보다 작으면 -> push||pop으로 그 값을 당장 빼오는것이 불가능함
> #### 지금 찾아야하는 값이 stack.back()보다 크면 -> stack.back()==tmp 가 될 때 까지 값을 push
> #### stack.back() == tmp 이면 stack.pop()을 통해 새로운 sequence에 push.
> #### 완성된 sequence는 찾고자 하는 stack_sequence일 것임.

## Code :
```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <string>

int main(){
    std::ios::sync_with_stdio(false);
    std::cin.tie(NULL);
    std::cout.tie(NULL);

    std::vector<int> stack;
    // std::vector<int> sequence;
    std::queue<int> buff;
    int N;
    int tmp;
    bool status = true;
    std::string result = "";
    std::cin >> N;
    for(int i=0; i<N; i++){
        std::cin >> tmp;
        buff.push(tmp);
    }

    int i=1;
    while(N!=0){
        tmp = buff.front();
        buff.pop();
        while(stack.empty() || stack.back() < tmp){
            stack.push_back(i++);
            result += "+\n";
        }
        if(stack.back() > tmp){
            std::cout << "NO" << std::endl;
            status = false;
            break;
        }
        if(stack.back()==tmp){
            // sequence.push_back(stack.back());
            stack.pop_back();
            result += "-\n";
            N--;
        }
        
    }
    if(status){
        std::cout << result;
    }
    // for(auto element : stack){
    //     std::cout << element << " ";
    // }
    // std::cout << std::endl;
    // for(auto element : sequence){
    //     std::cout << element << " ";
    // }
    // std::cout << std::endl;
}
```
> #### 답을 std::string 형식으로 반복문에서 출력하지 않고 한번에 출력하여 시간절약
> #### std::endl은 버퍼를 초기화시켜주는 작업을 포함하므로 수행속도가 느림 -> "\n"은 빠르니 사용해보자
