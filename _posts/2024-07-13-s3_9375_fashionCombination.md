---
title: "Baek Joon - s3_9375_fashionCombination"
categories: [PS]
tags: [] # TAG names should always be lowercase
---
## 문제 : 
> #### 의상:종류 데이터들이 주어졌을 때, 매일 다른 옷을 입는다고 한다면 며칠동안 옷을 입을 수 있을까

## 풀이 : #math
> #### 그냥 수학문제다. 경우의수를 판별하자

### IDEA :
> #### A종류 옷이 a개, B종류 옷이 b개 있다면 A종류 옷을 입지 않거나 뭐라도 입을 경우 총 a+1, B도 마찬가지로 b+1이므로 (a+1)x(b+1)-1이 옷을 입지 않는 총 경우의수
> #### 이런 아이디어를 착안해서 종류별로 옷을 나누고 각 종류 옷의 size+1만큼 서로 곱해준 후 -1 해주면 답이 나온다

## Code :
```cpp
#include <iostream>
#include <unordered_map>

int main(){
    int T;
    std::cin >> T;
    for(int i=0; i<T; i++){
        std::unordered_map<std::string, int> closet;
        int n;
        std::cin >> n;
        for(int i=0; i<n; i++){
            std::string a,b;
            std::cin >> a >> b;
            closet[b]++;
        }
        int cnt = 1;
        for(const auto& p : closet){
            cnt *= (p.second+1);
        }
        cnt--;
        std::cout << cnt << std::endl;
    }
}
```
