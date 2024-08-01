---
title: "Baek Joon - s5_11866_josephusPermutation"
categories: [PS]
tags: [] # TAG names should always be lowercase
---
## 문제 : 
> #### 요세푸스 순열을 구하는 문제.
> #### 요세프스 순열? :
> 요세프스 순열(Josephus permutation)은 유명한 수학적 문제로, n명의 사람이 원형으로 둘러앉아 있을 때, 어떤 특정한 규칙에 따라 한 명씩 제외시키면서 마지막에 남는 사람의 위치를 찾는 문제입니다. 이 문제는 역사적으로는 유대 역사책인 "유대역사"의 저자로 알려진 요세프스 플라비우스(Josephus Flavius)에게 귀속되어 "요세프스 순열"이라고 불리게 되었습니다.일반적인 형태로는 n명의 사람이 원형으로 둘러앉아 있고, 어떤 특정한 간격(k)만큼 떨어진 사람을 제외시키는 과정을 반복합니다. 이 과정을 반복하여 마지막에 남는 사람의 위치를 찾습니다.
> #### 수학적인 문제에서는 STL을 사용해보려는 생각을 가져봐야될듯함. `풀이1` 코드를 짜는 것 보다 `풀이2`코드를 짜는것이 훨씬 간결했고, 소요되는 시간이 적었음

## 풀이1 : #Full_implementation

### idea:
> #### 배열 공간을 할당하여 함수를 적절히 구현
> #### 삭제 대상을 가르키는 인덱스를 매 반복마다 update해주며 대상이 삭제되면 삭제된 element의 자리를 뒤따르는 element들을 앞당겨 재구조화

## Code1 :
```cpp
#include <iostream>
#include <vector>

class JP{
private:
    int* arr;
    std::vector<int> result;
    int size; //lsat index = size-1
    int pt;
    int step;
public:
    JP(int N, int K):size(N), step(K), pt(-1){
        arr = new int[N];
    }
    ~JP() {delete[] arr;}

    void init(){
        for(int i=0; i<size; i++){
            arr[i] = i+1;
        }
    }
    void update(){
        while(pt > size-1) pt = pt-size;
        result.push_back(arr[pt]);
        for(int i=pt; i<size-1; i++){
            arr[i] = arr[i+1];
        }
        pt --;
        size--;
    }
    void solve(){
        int permanent_size = size;
        for(int i=0; i<permanent_size; i++){
            pt += step; 
            update();
        }
        std::cout << "<";
        for(int i=0; i<permanent_size-1; i++){
            std::cout << result[i] << ", ";
        }
        std::cout << result[permanent_size-1] << ">" << std::endl;
    }
};

int main(){
    int N, K;
    std::cin >> N >> K;
    JP jp(N, K);
    jp.init();
    jp.solve();
}
```
----------------------------------------------
## 풀이2 : #Application_of_queue

### idea :
> #### queue를 응용
> #### 매 반복마다 push, pop 을 반복해가며 요세푸스 순열이 구해지는 로직을 구현

## Code2 : 
```cpp
#include <iostream>
#include <queue>

int main(){
    int N, K;
    std::cin >> N >> K;
    std::queue<int> jp;
    for(int i=0; i<N; i++){
        jp.push(i+1);
    }
    int tmp;
    std::cout << "<";
    for(int i=0; i<N-1; i++){
        for(int j=0; j<K-1; j++){
            tmp = jp.front();
            jp.pop();
            jp.push(tmp);
        }
        std::cout << jp.front() << ", ";
        jp.pop();
    }
    for(int j=0; j<K-1; j++){
        tmp = jp.front();
        jp.pop();
        jp.push(tmp);
    }
    std::cout << jp.front() << ">" << std::endl;

}
```
