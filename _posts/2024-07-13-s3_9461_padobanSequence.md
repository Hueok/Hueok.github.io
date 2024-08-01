---
title: "Baek Joon - s3_9461_padobanSequence"
categories: [PS]
tags: [] # TAG names should always be lowercase
---
## 문제 : 
> #### 첫 삼각형은 정삼각형으로 변의 길이는 1이다. 그 다음에는 다음과 같은 과정으로 정삼각형을 계속 추가한다. 나선에서 가장 긴 변의 길이를 k라 했을 때, 그 변에 길이가 k인 정삼각형을 추가한다.
> #### 파도반 수열 P(N)은 나선에 있는 정삼각형의 변의 길이이다.
> #### P(1)부터 P(10)까지 첫 10개 숫자는 1, 1, 1, 2, 2, 3, 4, 5, 7, 9이다. N이 주어졌을 때, P(N)을 구하는 프로그램을 작성하시오.

## 풀이 : #DP
> #### 점화식 찾기 문제.. 수열 패턴 찾기 문제와 다를바 없다

### IDEA :
> #### 조금 생각해보면 P(i) = P(i-1) + P(i-5) where p>5 임을 알 수 있다.
> #### 적당히 기초 값을 설정하고 iteration이든 recursion이든 구현해서 출력하자

## Code :
```cpp
#include <iostream>
#include <vector>

int main(){
    int T, N;
    std::cin >> T;
    std::vector<long> seq(101, 0);
    seq[1] = 1;
    seq[2] = 1;
    seq[3] = 1;
    seq[4] = 2;
    seq[5] = 2;
    for(int i=6; i<101; i++) seq[i] = seq[i-1] + seq[i-5];
    for(int i=0; i<T; i++){
        std::cin >> N;
        std::cout << seq[N] << std::endl;
    }
}
```
