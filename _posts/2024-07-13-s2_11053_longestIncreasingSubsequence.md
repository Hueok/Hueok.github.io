---
title: "Baek Joon - s2_11053_longestIncreasingSubsequence"
categories: [PS]
tags: [] # TAG names should always be lowercase
---
## 문제 : 
> #### 수열 A가 주어졌을 때, 가장 긴 증가하는 부분 수열을 구하는 프로그램을 작성하시오.
> #### 예를 들어, 수열 A = {10, 20, 10, 30, 20, 50} 인 경우에 가장 긴 증가하는 부분 수열은 A = {10, 20, 10, 30, 20, 50} 이고, 길이는 4이다.

## 풀이 : #DP
> #### 풀이가 너무 자명하다. 구현만 잘 하자

### IDEA : 
> #### lls : (i번째 수로 끝나는 LongesetIncreasingSubsequence의 길이를 저장함)
> #### lls를 DP기법으로 기저단계부터 채워나가는 loop절 적당히 구현
> #### i번째 llp를 채우려면 i-1이하의 모든 llp에 대해 탐색하고, increasing하는 조건인지 확인 한 후 llp[i]를 업데이트.

## Code :
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

int main(){
    int N;
    std::cin >> N;
    std::vector<int> arr(N, 0);
    std::vector<int> lls(N,1);
    for(int i=0; i<N; i++){
        std::cin >> arr[i];
    }
    for(int i=1; i<N; i++){
        for(int j=i-1; j>=0; j--){
            if(arr[j] < arr[i] && lls[j]+1 > lls[i]){
                lls[i] = lls[j]+1;
            }
        }
    }
    std::cout << *std::max_element(lls.begin(), lls.end()) << std::endl;
}
```
