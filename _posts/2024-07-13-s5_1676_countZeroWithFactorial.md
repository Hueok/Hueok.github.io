---
title: "Baek Joon - s5_1676_countZeroWithFactorial"
categories: [PS]
tags: [] # TAG names should always be lowercase
---
## 문제 : 
> #### 수`N!`의 맨 뒷자리 수 부터, 연속해서 나오는 0의 개수 구하기

## 풀이 : #약간greedy?
> #### 간단한 수학적 아이디어를 생각하고, 간단하게 구현하는느낌..

### idea : 
> #### N=500일 때, N!이 1.0e+1000이 넘어가기 때문에 std::string::to_string()으로 뒷자리부터 카운팅하는 것이 불가능함
> #### 0의 개수는 수 N!이 갖는 인수`2x5`의 차수로 계산될 수 있으므로, min(2의 인수, 5의 인수)가 답이 된다.
> #### 따라서 2의 인수와 5의 인수를 구하는 간단한 문제로 바뀐다.

## Code :
```cpp
#include <iostream>
#include <cmath>

int main(){
    int N;
    int two_factor =0;
    int five_factor = 0;
    std::cin >> N;
    for(int i=1; i<N+1; i++){
        int num = i;
        while(num%2 == 0){
            num = num/2;
            two_factor++;
        }
        while(num%5 == 0){
            num = num/5;
            five_factor++;
        }
    }
    std::cout << std::min(two_factor, five_factor) << std::endl;

}
```
