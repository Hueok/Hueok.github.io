---
title: "Baek Joon - g5_10026_colorBlindness"
categories: [PS]
tags: [] # TAG names should always be lowercase
---
## 문제 :
> #### A little known fact about cows is the fact that they are red-green colorblind, meaning that red and green look identical to them.  This makes it especially difficult to design artwork that is appealing to cows as well as humans.
> #### Consider a square painting that is described by an N x N grid of characters (1 <= N <= 100), each one either R (red), G (green), or B (blue).  A painting is interesting if it has many colored "regions" that can be distinguished from each-other.  Two characters belong to the same region if they are directly adjacent (east, west, north, or south), and if they are indistinguishable in color.  For example, the painting
> ```
> RRRBB
> GGBBB
> BBBRR
> BBRRR
> RRRRR
> ```
> #### has 4 regions (2 red, 1 blue, and 1 green) if viewed by a human, but only 3regions (2 red-green, 1 blue) if viewed by a cow.  
> #### Given a painting as input, please help compute the number of regions in the painting when viewed by a human and by a cow.

## 풀이 : #BFS
> #### Hard Coding 한것같다.

### IDEA :
> #### 단순히 적록 색약을 가진 사람이 보는 그림과, 모든 색을 구별할 수 있는 사람이 보는 그림을 따로 저장해서 BFS를 두번 수행했다.
> #### 메모리 제한이 널널해서 가능한 방법인듯 하다.

## Code :
```cpp
#include <iostream>
#include <string>
#include <vector>
#include <queue>

std::vector<std::string> picture_general;
std::vector<std::string> picture_blind;
std::vector<std::vector<bool>> visited;

int N;
int D[4] = {1, -1, 0, 0};
void diffusion(const std::pair<int, int>& pt, std::vector<std::string>& picture){
    std::queue<std::pair<int, int>> q;
    char c = picture[pt.first][pt.second];
    q.push({pt.first, pt.second});
    while(!q.empty()){
        int x = q.front().first;
        int y = q.front().second;
        q.pop();
        for(int i=0; i<4; i++){
            int nx = x + D[i];
            int ny = y + D[3-i];
            if(nx<0 || nx>=N || ny<0 || ny>=N || visited[nx][ny] || c!=picture[nx][ny]) continue;
            q.push({nx, ny});
            visited[nx][ny] = true;
        }
    }
}

int main(){
    std::cin >> N;
    picture_general = std::vector<std::string>(N);
    picture_blind = std::vector<std::string>(N);
    visited = std::vector<std::vector<bool>>(N, std::vector<bool>(N, false));
    for(int i=0; i<N; i++){
        std::string raw;
        std::cin >> raw;
        picture_general[i] = raw;
        for(int i=0; i<raw.size(); i++){
            if(raw[i] == 'G') raw[i]='R';
        }
        picture_blind[i] = raw;
    }
    int region_general = 0;
    int region_blind = 0;
    for(int i=0; i<N; i++){
        for(int j=0; j<N; j++){
            if(visited[i][j]) continue;
            diffusion({i, j}, picture_general);
            region_general++;
        }
    }
    visited = std::vector<std::vector<bool>>(N, std::vector<bool>(N, false));
    for(int i=0; i<N; i++){
        for(int j=0; j<N; j++){
            if(visited[i][j]) continue;
            diffusion({i, j}, picture_blind);
            region_blind++;
        }
    }
    std::cout << region_general << " " << region_blind << std::endl;

}
```
