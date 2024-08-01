---
title: "Baek Joon - b2_2231_decomposeSumNumber"
categories: [PS]
tags: [] # TAG names should always be lowercase
---
## 문제 :
> #### M을 분해합하면 N이 나온다고 할 때, M을 M의 생성자라고 하자. 이때, N<1e+06 이 주어지면 이것의 생성자 중 가장 작은것을 구하라.

## 풀이 : #bruteforce
> #### 모든 케이스에 대하여 자릿수 더하는 로직만 만들면 됨
> #### std:;string을 이용하여 이 로직을 구현했지만, 나머지, 몫을 이용하여 숫자만을 이용해서도 풀이가 가능할듯

## Code :
```cpp
#include <iostream>
#include <string>
#include <set>

int main(){
    int N;
    std::cin >> N;
    std::set<int> sol;
    for(int i=1; i<N; i++){
        int result = i;
        std::string reconstruct = std::to_string(i);
        for(int j=0; j<reconstruct.size(); j++){
            int tmp = std::stoi(std::string(1, reconstruct[j]));
            result += tmp;
        }
        if(result == N){
            sol.insert(i);
        }
    }
    std::cout << *sol.begin() << std::endl;
}
```
