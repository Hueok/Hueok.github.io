---
title: "Baek Joon - g5_16928_snakesAndLadders"
categories: [PS]
tags: [] # TAG names should always be lowercase
---
## 문제 :
> #### 게임은 정육면체 주사위를 사용하며, 주사위의 각 면에는 1부터 6까지 수가 하나씩 적혀있다. 게임은 크기가 10×10이고, 총 100개의 칸으로 나누어져 있는 보드판에서 진행된다. 보드판에는 1부터 100까지 수가 하나씩 순서대로 적혀져 있다.
> #### 플레이어는 주사위를 굴려 나온 수만큼 이동해야 한다. 예를 들어, 플레이어가 i번 칸에 있고, 주사위를 굴려 나온 수가 4라면, i+4번 칸으로 이동해야 한다. 만약 주사위를 굴린 결과가 100번 칸을 넘어간다면 이동할 수 없다. 도착한 칸이 사다리면, 사다리를 타고 위로 올라간다. 뱀이 있는 칸에 도착하면, 뱀을 따라서 내려가게 된다. 즉, 사다리를 이용해 이동한 칸의 번호는 원래 있던 칸의 번호보다 크고, 뱀을 이용해 이동한 칸의 번호는 원래 있던 칸의 번호보다 작아진다.
> #### 게임의 목표는 1번 칸에서 시작해서 100번 칸에 도착하는 것이다.
> #### 게임판의 상태가 주어졌을 때, 100번 칸에 도착하기 위해 주사위를 굴려야 하는 횟수의 최솟값을 구해보자.

## 풀이 : #bfs
> #### Down-top with recursion 알고리즘 실패... -> bfs접근

### IDEA : 
> #### 인접노드를 정의할 수 있다는점에서 BFS로 접근 가능하다는 의미다.
> #### SP 문제에서 BFS전략을 쓰듯 유연하게 접근하자
> #### 인접노드는 (i+1)~(i+6)인데, 인접노드로 접근할 때 그 노드가 Snakes 혹은 Ladders의 시작점인지 확인해야한다.
> #### 만약 Snakes 또는 Ladders라면 그 노드는 시작점에서 정의되지 않고 도착점에서 정의된다. 즉, 4->7 사다리가 있다면 4번노드로 접근할 때 반드시 7번노드로 대체 되어야한다.
> #### 노드를 방문처리 해 가면서 깊이를 기록하고, 목적지까지의 Distance of Shortest Path를 구하자.

## Code :
```cpp
#include <iostream>
#include <queue>
#include <vector>
#include <map>

int main(){
    int N, M;
    std::cin >> N >> M;
    std::map<int, int> tp;
    for(int i=0; i<N+M; i++){
        int origin, dest;
        std::cin >> origin >> dest;
        tp[origin] = dest;
    }
    std::vector<int> visited(101, 0);
    std::queue<int> q;
    q.push(1);
    visited[1] = 1;
    while(!q.empty()){
        int n = q.front();
        q.pop();
        for(int i=1; i<=6; i++){
            int next = tp[n+i] ? tp[n+i] : n+i;
            if(next > 100 || visited[next]) continue;
            visited[next] = visited[n] + 1;
            q.push(next);
        }
    }
    std::cout << visited[100] -1 << std::endl;
}
```
---------------------------------
## Study with External Post..
> #### 내가 처음으로 접근했던 Down-top DP 전략과 비슷한 풀이가 존재했다.
> #### 내가 실패한 이유는 Down-Top Recursion으로 basis value를 정의하고 나서, 차례차례 기저단계의 정의가 마무리 된 이후에, 그보다 높은 계층에 있는 값이 바뀜으로 나타나는 저단계 값 변화를 커버할 수 없기 때문이었다.
> #### 그러나 이를 비슷한 방법으로 접근하고 해결하는 로직이 존재했다!
> #### DP전략의 장점은 살릴 수 없지만 Update형태의 Recursive function을 정의해서 값이 갱신 되는 조건이 있을 때마다 해당 칸으로 부터 파생가능한 모든 경우를 Update해주는 방법으로 풀 수 있다.
> #### -> 이는 DP보다는 Brute Force에 가까운 전략이다.

## Code :
```cpp
#include <iostream>
#include <vector>
#include <map>
#include <algorithm>

std::vector<int> arr(101, 1e9);
std::map<int, int> gates;

void printArr(){
    for(int i=1; i<101; i++){
        std::cout << i << " : " << arr[i] << std::endl;
    }
    std::cout << std::endl;
}

void update(int pos, int depth){
    // std::cout << "current pos : " << pos << std::endl;
    if(arr[pos]<depth) return;
    arr[pos] = depth;
    if(gates[pos]){
        update(gates[pos], depth);
        return;
    }
    for(int i=1; i<=6; i++){
        if(pos+i <= 100 && arr[pos+i]>depth+1) update(pos+i, depth+1);
    }
}

int main(){
    int N, M;
    std::cin >> N >> M;
    for(int i=0; i<N+M; i++){
        int dest, origin;
        std::cin >> origin >> dest;
        gates[origin] = dest;
    }
    update(1,0);
    std::cout << arr[100] << std::endl;
    // printArr();

}
```
