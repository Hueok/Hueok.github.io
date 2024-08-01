---
title: "Baek Joon - s5_10814_sortWithComparator"
categories: [PS]
tags: [] # TAG names should always be lowercase
---
## 문제 
> 나이순 + 등록 순으로 sort하는 문제


## 풀이 : #implementation
> 이 문제에서 배운것이 무척 많다. 

### solve idea
> #### map을 이용하여 사용자 정보를 저장하려 했으나, 등록 순서가 필요하므로 vector를 활용함.
> #### std::sort()의 마지막 인자에 compare()함수를 만들어 추가하려했으나, 등록 순서라는 추가정보가 필요하여 구현에 어려움이 있었음.
> #### -> 마지막 인자에 function 그 자체뿐만 아니라, overriding 된 operator() 멤버함수를 갖는 객체 또한 파라미터로 줄 수 있음을 알게됨.
> #### -> 이를 활용하여 arr정보를 class 내에 저장하고 getIndex() inner function을 정의하여 operator()함수 내에서 다양한 정보를 사용할 수 있게 만듬.
> #### 그러나 getIndex()함수는 time complexity O(n)을 갖기때문에, quickSort와 함께라면 time complexity O(n^2 logn)를 갖게되어 시간초과 발생.
> #### 따라서 getIndex()함수를 호출하지 않고도 index정보를 arr내에 저장할 수 있어야 했음
> #### -> vector 내에 pair를 저장할 때, pair 내의 요소로 또다른 pair를 갖게 함으로써 2차원 배열과 유사한 형태를 띄게할 수 있음 -> 바깥 pair의 first정보에 index를 저장할 수 있음!!
> #### 또한, iostream의 다중호출 가능성이 있으므로 sync_with_stdio(false)와 cin.tie(NULL), cout,tie(NULL)을 호출하고, 최종적인 result를 string에 담아 cout이 여러번 호출되지 않도록하여 소요 시간을 줄임



## Code 
```cpp
#include <iostream>
#include <vector>
#include <utility>
#include <algorithm>

class Compare{
public:
    bool operator()(const std::pair<int, std::pair<int, std::string>>& person1, const std::pair<int, std::pair<int, std::string>>& person2){
        if(person1.second.first == person2.second.first){
            return person1.first < person2.first;
        }else{
            return person1.second.first < person2.second.first;
        }
    }
};

int main(){
    std::ios::sync_with_stdio(false); std::cin.tie(NULL); std::cout.tie(NULL);
    int N;
    std::cin >> N;

    std::vector<std::pair<int, std::pair<int, std::string>>> arr;

    int age;
    std::string name;
    for(int i=0; i<N; i++){
        std::cin >> age >> name;
        arr.push_back(std::make_pair(i, std::make_pair(age, name)));
    }
    std::sort(arr.begin(), arr.end(), Compare());

    std::string result = "";
    for(auto& element : arr){
        result += std::to_string(element.second.first) + " " + static_cast<std::string>(element.second.second) + "\n";
        //std::cout << element.second.first << " " << element.second.second << std::endl;
    }
    std::cout << result << std::endl;

    return 0;

}
```
### Compare 객체 최적화 이전 코드
```cpp
 class Compare {
     public:
         std::vector<std::pair<int, std::string>> arr_;
        
     public:
         Compare(const std::vector<std::pair<int, std::string>>& arr)
         :arr_(arr)
         {}
         int getIndex(const std::pair<int, std::string>& target){
             auto it = std::find(arr_.begin(), arr_.end(), target);
             return std::distance(arr_.begin(), it);
         }
         bool operator()(const std::pair<int, std::string>& person1, const std::pair<int, std::string>& person2){
             if(person1.first == person2.first){
                 return getIndex(person1) < getIndex(person2);
             }else{
                 return person1.first < person2.first;
             }
         }
 };
```
-------------------------------------------
## 풀이: #inplementation with "stable_sort"
> stable_sort는 merge_sort기반이다. -> 컨테이너가 갖고있는 기존의 순서를 깨지않는다.

## Code
```cpp
#include <iostream>
#include <vector>
#include <algorithm>

bool compare(const std::pair<int, std::string>& person1, const std::pair<int, std::string>& person2){
    return person1.first < person2.first;
}


int main(){
    std::ios::sync_with_stdio(false);
    std::cin.tie(NULL); std::cout.tie(NULL);
    int N;
    std::cin >> N;
    std::vector<std::pair<int, std::string>> arr;
    int age; 
    std::string name;

    for(int i=0; i<N; i++){
        std::cin >> age >> name;
        arr.push_back(std::make_pair(age, name));
    }
    std::stable_sort(arr.begin(), arr.end(), compare);
    std::string result = "";
    for(auto& element : arr){
        result += std::to_string(element.first) + " " + element.second + "\n";
    }
    std::cout << result << std::endl;
    return 0;
}


```
