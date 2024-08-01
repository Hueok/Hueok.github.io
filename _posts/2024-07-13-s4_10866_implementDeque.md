---
title: "Baek Joon - s4_10866_implementDeque"
categories: [PS]
tags: [] # TAG names should always be lowercase
---
## 문제 :
> #### 크기가 주어진 Deque를 구현하는 문제
> #### 딱히 얻어가는건 없는 문제...

## 풀이 : #Full_implementation
> #### STL을 사용하지 풀 구현하는 방법.

### idea : 
> #### stack, deque 구현 문제와 다르게 배열의 시작과 끝에서 모두 값이 추가될 수 있으므로 `2N+1`사이즈의 배열을 할당
> #### 적당한 멤버 함수로 기능 구현

## Code : 
```cpp
#include <iostream>

class Deque{
private:
    int* arr;
    int front_index;
    int back_index;

public:
    Deque(int N):front_index(N), back_index(N+1){
        arr = new int[2*N + 1];
    }
    ~Deque(){
        delete[] arr;
    }

    void push_front(int arg){
        arr[front_index--] = arg;
    }
    void push_back(int arg){
        arr[back_index++] = arg;
    }
    void pop_front(){
        if(empty()) std::cout << -1 << std::endl;
        else{
            std::cout << arr[++front_index] << std::endl;
        }
    }
    void pop_back(){
        if(empty()) std::cout << -1 << std::endl;
        else {
            std::cout << arr[--back_index] << std::endl;
        }
    }
    void size(){
        std::cout << back_index - front_index - 1 << std::endl;
    }
    bool empty(){
        if(front_index+1 == back_index) return true;
        else return false;
    }
    void front(){
        if(empty()) std::cout << -1 << std::endl;
        else{
            std::cout << arr[front_index+1] << std::endl;
        }
    }
    void back(){
        if(empty()) std::cout << -1 << std::endl;
        else{
            std::cout << arr[back_index-1] << std::endl;
        }
    }

};

int main(){
    std::ios::sync_with_stdio(false);
    std::cin.tie(NULL); std::cout.tie(NULL);

    int N;
    std::cin >> N;

    Deque deque(N);

    std::string command;
    int arg;

    for(int i=0; i<N; i++){
        std::cin >> command;
        if(command == "push_front") {
            std::cin >> arg;
            deque.push_front(arg);
        }
        else if(command == "push_back"){
            std::cin >> arg;
            deque.push_back(arg);
        }
        else if(command == "pop_front") deque.pop_front();
        else if(command == "pop_back") deque.pop_back();
        else if(command == "size") deque.size();
        else if(command == "empty"){
            int result = deque.empty() ? 1 : 0;
            std::cout << result << std::endl;
        }
        else if(command == "front") deque.front();
        else if(command == "back") deque.back();
    }
}
```
