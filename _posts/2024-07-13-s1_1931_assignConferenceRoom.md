---
title: "Baek Joon - s1_1931_assignConferenceRoom"
categories: [PS]
tags: [] # TAG names should always be lowercase
---
## 문제 : 
> #### 사용할 수 있는 회의의 최대 개수를 출력한다.

## 풀이 : #Greedy
> #### 문제에 제시되어있지 않은 조건때문에 오답이 발생하는 특수 케이스들이 존재하니... 좋은 문제는 아닌것 같다.

### IDEA :
> #### 회의가 빨리 끝나는 순으로 정렬
> #### 해당 회의가 끝난 시간으로부터 가장 인접해있는 시작 시간을 갖는 회의를 선택한다.

## Code :
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

bool comp(const std::pair<int, int>& lhs, const std::pair<int, int>& rhs){
    if(lhs.second == rhs.second){
        return lhs.first < rhs.first;
    }
    return lhs.second < rhs.second;
}
int main(){
    std::ios::sync_with_stdio(false);
    std::cin.tie(NULL);
    int N;
    std::cin >> N;
    std::vector<std::pair<int, int>> schedules;
    //<startTime, endTime>
    for(int i=0; i<N; i++){
        int a, b;
        std::cin >> a >> b;
        schedules.push_back({a, b});
    }
    std::sort(schedules.begin(), schedules.end(), comp);
    // schedules.erase(std::unique(schedules.begin(), schedules.end()), schedules.end());
    // for(const auto& element : schedules){
    //     std::cout << element.first << ", " << element.second << std::endl;
    // }
    int cnt = 1;
    int time = schedules.front().second;
    for(int i=1; i<schedules.size(); i++){
        if(schedules[i].first >= time){
            cnt++;
            time = schedules[i].second;
        }
    }
    std::cout << cnt << std::endl;
}
```
