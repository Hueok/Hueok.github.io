---
title: "Baek Joon - s2_18870_coordinateCompression"
categories: [PS]
tags: [] # TAG names should always be lowercase
---
## 문제 : 
> #### Xi를 좌표 압축한 결과 X'i의 값은 Xi > Xj를 만족하는 서로 다른 좌표 Xj의 개수와 같아야 한다. X1, X2, ..., XN에 좌표 압축을 적용한 결과 X'1, X'2, ..., X'N를 출력해보자.

## 풀이 : #Hash_Table
> #### 깐깐한 timeout 기준을 맞춰주자

### IDEA :
> #### 입력된 수열을 set에 담아서 중복을 없엠과 동시에 정렬한다.
> #### 처음 입력된 수열을 순회하면서 각 요소가 set컨테이너의 몇번째에 위치하는지 출력하면 된다.
> #### -> 이 인덱스를 찾는 과정에서 Timeout 여부가 결정되는데, BST기반의 set에서 std::find()했을 때 O(log N)인줄 알고 시도했으나 time out 발생.
> #### -> 이후 std::find()를 피하기 위해 추가 메모리가 필요하지만 요소의 인덱스를 저장하는 새로운 mapping관계를 만들어 std::find()를 대신함 -> success

## Code :
```cpp
#include <iostream>
#include <vector>
#include <map>
#include <set>
#include <algorithm>
#include <string>

int main(){
    std::ios::sync_with_stdio(false);
    std::cin.tie(NULL); std::cout.tie(NULL);
    int N;
    std::cin >> N;
    std::vector<int> arr(N);
    std::set<int> sorted;
    for(int i=0; i<N; i++){
        std::cin >> arr[i];
        sorted.insert(arr[i]);
    }
    std::map<int, int> positions;
    // <value, order>
    int pos=0;
    for(const auto& element : sorted){
        positions[element] = pos++;
    }
    std::string result = "";
    for(const auto& element : arr){
        result += std::to_string(positions[element]) + " ";
    }
    std::cout << result << std::endl;
}
```
#### 문제풀이 후 알게되었는데, std::sort()는 어느 컨테이너를 대상으로는 Linear Search를 수행하므로 `O(N)`이다. std::set을 대상으로 `O(log N)`을 기대하기 위해서는 std::set::find()를 사용해야한다.
#### 또, std::distance() 자체도 일반적으로 `O(N)`이다. 그러나 만약 Container가 Random Access Iterator를 제공한다면, `O(1)`이다.
#### std::distance()를 대신하기 위해 두 반복자를 구해서 빼는 연산을 하려면 해당 컨테이너가 Random Access Iterator를 제공해야한다. 따라서 BST기반의 std::set에서는 std::distance()만 쓸 수 있다.
#### -> 처음 작성한 코드는 std::set을 대상으로 일반 탐색인 std::find()를 사용하였고, Bidirectioanl Iterator만을 제공하는 컨테이너에 std::distance()를 사용했으므로 당연히 시간 초과가 나는것이다.
