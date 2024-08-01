---
title: "Baek Joon - g5_1107_remoteControl"
categories: [PS]
tags: [] # TAG names should always be lowercase
---
## 문제 : 
> #### 리모컨의 특정 버튼들이 고장났을 때, 목적 채널로 이동하기 위해 필요한 최소한의 버튼 클릭 횟수를 구하라.

## 풀이 : #bruteforce
> #### 특수 케이스를 생각해내고 핸들링하는것이 핵심..

### Idea : 
> #### BinarySearch를 먼저 생각했지만 시간조건이 2s이고, N<500,000 이므로 LinearSearch로도 충분.
> #### 예외 케이스를 생각해내지 못해서 시간을 얼추 썼다... 백준의 질문 게시판을 통해 많은 케이스들을 테스트 하면서 문제를 해결할 수 있었다.

## Code :
```cpp
#include <iostream>
#include <unordered_set>
#include <cstdlib>

int main(){
    int target_channel, current_channel, ban_count;
    std::unordered_set<char> ban_list;
    std::cin >> target_channel >> ban_count;
    char tmp;
    int analogy = -2000000000;
    int result;
    for(int i=0; i<ban_count; i++){
        std::cin >> tmp;
        ban_list.insert(tmp);
    }
    for(int i=0; i<1000001; i++){
        std::string str = std::to_string(i);
        bool status = true;
        for(int j=0; j<str.size(); j++){
            if(ban_list.find(str[j]) != ban_list.end()){
                status = false;
                break;
            }
        }
        if(status && std::abs(target_channel-i) < std::abs(target_channel-analogy)) analogy = i;
    }
    // std::cout << "ANALOGY : " << analogy << std::endl;
    result = std::to_string(analogy).size() + std::abs(target_channel-analogy);
    if(result > std::abs(target_channel-100)) result = std::abs(target_channel-100);
    std::cout << result << std::endl;
}
```
