---
title: "Baek Joon - s4_10816_searchElements"
categories: [PS]
tags: [] # TAG names should always be lowercase
---
## 문제
#### 두 정수 집합 숫자 세트 A, B가 주어졌을 때, B의 각 엘리먼트가 A에 몇개씩 존재하는지 출력하는 문제. (N,M < 500000)

## 풀이1 : #hash_table
> 이 방법은 TimeOut 발생. (이유를 아직 모르겠음)

### idea : 
> #### unordered_set이 hash table 기반이므로, 집합 A에 대해서는 unordered_set을 사용하여 search를 빠르게 수행.
> #### 집합 B에 대해서는 굳이 데이터를 store할 필요 없으므로, 입력되는 값을 즉시 count의 인자로 사용.

## Code1 :
```cpp
#include <iostream>
#include <unordered_set>
#include <algorithm>
#include <vector>

int main(){
    std::ios::sync_with_stdio(false);
    std::cin.tie(NULL);
    std::cout.tie(NULL);

    int N, M;

    std::cin >>N;
    std::unordered_multiset<int> fixedNum;
    int tmp;
    for(int i=0; i<N; i++){
        std::cin >> tmp;
        fixedNum.insert(tmp);
    }
    std::cin >> M;
    std::string result = "";
    for(int i=0; i<M; i++){
        std::cin >> tmp;
        // result += std::to_string(fixedNum.count(tmp)) + " ";
        std::cout << fixedNum.count(tmp) << " ";
    }
    // std::cout << result << std::endl;

    return 0;
}
```
--------------------------------------------

## ___풀이2 : #Hash_table_implementation___
> 1번 방법은 unordered_set + count 를 활용했지만, 2번은 count 없이 map을 통해 값을 직접 참조
> time complexity 면에서 1번과 다를바 없다고 생각했지만 이 풀이는 time out이 안났음.
> -> 유일하게 성공한 방법

### idea :
> #### unordered_map 또한 hash table 기반이므로, count함수를 사용하지 않고도 1번 방법과 유사한 로직을 구축할 수 있음.
> #### A에 없는 정수값을 레퍼런싱 할 때 자동으로 pair가 생성되는 map의 특징을 이용해서 0번 출현한 정수에 대해서도 정상적인 output을 출력 가능.

## Code2 :
```cpp
#include <iostream>
#include <unordered_map>

int main(){
    std::unordered_map<int, int> m;
    int N, M, card;

    std::ios::sync_with_stdio(false);
    std::cin.tie(NULL);

    std::cin >> N;
    for(int i=0; i<N; i++){
        std::cin >> card;
        m[card]++;
    }
    std::cin >> M;
    for(int i=0; i<M; i++){
        std::cin >> card;
        std::cout << m[card] << " ";
    }
}
```
------------------------------------------------------

## 풀이3 : #BinarySearchTree
> 왜인지 모르겠으나 또 TimeOut 발생. <br>
> multiset을 이용하지 않고, 일반적인 array[500001]을 선언하고 선 sort 후 distance를 계산하면 timeOut 안난다고 하는데, 내 방법은 왜 안되는지 아직 이해가 안됨. <br>
> O(M logN) : O(logN + M*(N+2logN)) 이라서 그런가? 계수를 따지면 더 느려지나? <br>

### idea : 
> #### std::multiset은 BST기반 컨테이너 이므로 element를 insert할 때 알아서 sort됨. (Red-black tree 형태는 모든 연산이 time complexity of log가 되게 보장함)
> #### + : 이제와서 하는 생각이지만 std::lower_bound()는 대상 컨테이너가 array던 multiset이던 상관없이 O(logN)을 가지는데, 굳이 insert할 떄마다 O(logN)을 추가로 갖는 multiset을 쓸 필요는 없을듯...

### learned :
> #### `std::lower_bound()`, `std::upper_bound()` in `<algorithm>` are both have time complexity of `O(log N)`.
> #### `std::lower_bound()` return iterator of position whose value is equal or bigger than target.
> #### `std::upper_bound()` return iterator of position whose value is bigger than target.
> #### If iterator of container provide RandomAccessIterators, then -> `operator-` is valid.
> #### else if (which provide only Bidrectional Iterators), then -> `operator-` unvalid. -> have to use `std::distance`

## Code3 :
```cpp
#include <iostream>
#include <set>
#include <algorithm>

int main(){
    std::ios::sync_with_stdio(false);
    std::cin.tie(NULL);

    int N, M, tmp;
    std::cin >> N;
    std::multiset<int> fixedNum;
    for(int i=0; i<N; i++){
        std::cin >> tmp;
        fixedNum.insert(tmp);
    }
    std::cin >> M;
    for(int i=0; i<M; i++){
        std::cin >> tmp;
        std::cout << std::distance(std::lower_bound(fixedNum.begin(), fixedNum.end(), tmp), std::upper_bound(fixedNum.begin(), fixedNum.end(), tmp)) << " " ;
    }

}
```





