---
title: "Baek Joon - s1_2667_numberingComplex"
categories: [PS]
tags: [] # TAG names should always be lowercase
---
## 문제 :
> #### 지도를 입력하여 단지수를 출력하고, 각 단지에 속하는 집의 수를 오름차순으로 정렬하여 출력하는 프로그램을 작성하시오.

## 풀이 : #BFS
> #### Well-known. BFS로 구현만 착실히.

### IDEA :
> #### 방문 데이터가 딱히 필요없다. 집 정보의 원본이 유지되어야 할 필요가 딱히 없기 때문에 이를 조작하여 방문여부로 생각할 수 있다.
> #### BFS로 단지 하나를 탐색하여 그 단지에 집이 얼마나 있는지 리턴하는 함수를 만들자. 만약 맵에 집이 존재하면 이 함수에 집의 좌표를 입력해 맵을 수정하며 결과를 쌓아나가자.

## Code :
```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <string>
#include <algorithm>


std::vector<std::string> map;
int N;
int getComplex(const std::pair<int, int>& pt){
    int result = 1;
    std::queue<std::pair<int, int>> q;
    int D[4] = {1, -1, 0, 0};
    q.push(pt);
    map[pt.first][pt.second] = '0';
    while(!q.empty()){
        std::pair<int, int> n = q.front();
        q.pop();
        for(int i=0; i<4; i++){
            int nx = n.first + D[i];
            int ny = n.second + D[3-i];
            if(nx<0 || nx>=N || ny<0 || ny>=N || map[nx][ny]=='0') continue;
            q.push({nx, ny});
            map[nx][ny] = '0';
            result ++;
        }
    }
    return result;
}

void printComplex(std::vector<std::string>& map){
    for(const auto& element : map){
        std::cout << element << std::endl;
    }
}

int main(){
    std::cin >> N;
    map = std::vector<std::string>(N);
    std::vector<int> complexList;
    for(int i=0; i<N; i++){
        std::cin >> map[i];
    }
    for(int i=0; i<N; i++){
        for(int j=0; j<N; j++){
            if(map[i][j] == '1'){
                complexList.push_back(getComplex({i, j}));
            }
        }
    }
    std::sort(complexList.begin(), complexList.end());
    std::cout << complexList.size() << std::endl;
    for(const auto& element : complexList){
        std::cout << element << std::endl;
    }
}
```
#### printComplex()함수는 테스트용 함수이다. 풀이에는 영향이 없다.
