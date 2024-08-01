---
title: "Baek Joon - g4_7662_dualPriorityQueue"
categories: [PS]
tags: [] # TAG names should always be lowercase
---
## 문제 :
> #### 이중 우선순위 큐를 구현하라. delete 연산은 가장 큰 값과 가장 작은 값을 제거하는 두개의 연산으로 나뉘고, append 연산은 큐에 요소를 추가한다.

## 풀이 : #multiset
> #### 꼼수지만 BST기반의 multiset컨테이너를 이용하면 시간초과 걱정없이 잘 구현할 수 있다.

### IDEA :
> #### 아이디어랄게 없다. 이 풀이에서는 그저 multiset이 제공하는 기능들을 Control Flow에 맞게 잘 이용하자

## Code :
```cpp
#include <iostream>
#include <set>

class DPQ{
public:
    std::multiset<int> arr;
public:
    void in(int val){
        arr.insert(val);
    }
    void out(int sign){
        if(isEmpty()) return;
        if(sign<0){
            arr.erase(arr.begin());
        } else{
            arr.erase(--arr.end());
        }
    }
    bool isEmpty(){
        return arr.empty();
    }
    void printResult(){
        if(isEmpty()) std::cout << "EMPTY" << std::endl;
        // else if(arr.size() == 1) std::cout << *(arr.begin()) << std::endl;
        else{
            std::cout << *(--arr.end()) << " " << *(arr.begin()) << std::endl;
        }
    }
};

int main(){
    std::ios::sync_with_stdio(false);
    std::cin.tie(NULL); std::cout.tie(NULL);
    int T;
    std::cin >> T;
    while(T--){
        DPQ q;
        int k;
        std::cin >> k;
        char command;
        int arg;
        while(k--){
            std::cin >> command >> arg;
            if(command == 'I') q.in(arg);
            else if(command == 'D') q.out(arg);
        }
        q.printResult();
    }

}
```
--------------------------------
#### Data Structure를 공부하는 입장에서 이 문제를 Full Implementation으로도 접근해봤다.

### IDEA :
> #### 처음 생각한것은, Heap영역에 부여된 메모리의 순서를 바꾸지 않고, MaxHeap, MinHeap을 구현하는 두가지 구조체를 선언하는 것이었다.
> #### 이것이 가능한 이유는 포인터 때문이다. MaxHeap, MinHeap에 맞게 포인터들만 재배열한 객체를 생성하여 하나의 객체에서 포인터가 삭제되면 다른것에 자동으로 영향을 주게끔 하려고했다.
> #### 그러나 이러한 구현은 Dangling Pointer의 위험성이 존재하면서도, 결국에 해당 노드에서 Heapify해주어야 한다는점이 포인터를 쓰지 않을때와 비교해서 이점이 없었다.
> #### 따라서 MaxHeap과 MinHeap 두가지를 정석적으로 구현하고 하나의 객체에서 값이 제거되면 나머지 객체에서 값을 찾아 제거하는 식으로 로직을 생각했다.
> #### 나머지 객체에서 해당 값이 어디에 위치하는지 탐색하는 프로세스에서 Time Complexity를 고려하자.
> #### Liear Search로 차례로 탐색하면 늦는다.
> #### 따라서 모든 value에 고윳값을 부여하고 이 고유값을 key값으로 갖는 Hash Table을 구현하여 어떤 고윳값의 위치를 value에 저장했다.
> #### 주의할 점은 Heapify등 힙 구조 내부의 값에 순서변화가 생긴다면 이 Hash Table도 같은 로직으로 순서변화를 일으켜야한다는 점이다.

## Code :
```cpp
#include <iostream>
#include <vector>
#include <unordered_map>

#define MAX_SIZE 1000002

class Heap{
public:
    std::vector<std::pair<int, int> > arr; //<value, id>
    std::unordered_map<int, int> indices;  //<id, to_del_idx>
    int id;
    int end;
public:
    Heap():end(1), id(1){
        arr.resize(MAX_SIZE);
    }
    friend void swap(std::pair<int, int>& lhs, std::pair<int, int>& rhs);
    void append(int value);
    int pop();
    void del(int target_id);
    virtual void UpwardHeapify(int n){}
    virtual void DownwardHeapify(int n){}
};
void swap(std::pair<int, int>& lhs, std::pair<int, int>& rhs){
    std::pair<int, int> tmp = lhs;
    lhs = rhs;
    rhs = tmp;
}
class MinHeap:public Heap{
public:
    MinHeap():Heap(){};
    void UpwardHeapify(int n);
    void DownwardHeapify(int n);
};
class MaxHeap : public Heap{
public:
    MaxHeap():Heap(){};
    void UpwardHeapify(int n);
    void DownwardHeapify(int n);
};

void Heap::append(int value){
    arr[end].first = value;
    arr[end].second = id;
    indices[id++]=end;
    UpwardHeapify(end++);
}
int Heap::pop(){
    if(end==1) return 0;
    int target_id = arr[1].second;
    arr[1] = arr[--end];
    DownwardHeapify(1);
    return target_id;
}
void Heap::del(int target_id){
    int idx = indices[target_id];
    if(idx == end-1){
        end--;
        indices.erase(target_id);
        return;
    }
    arr[idx] = arr[end-1];
    indices[arr[idx].second] = idx;
    end--;
    indices.erase(target_id);
    UpwardHeapify(idx);
}

void MinHeap::UpwardHeapify(int n){
    int idx = n;
    int parent = idx/2;
    while(parent!=0 && arr[idx].first<arr[parent].first){
        swap(arr[idx], arr[parent]);
        indices[arr[idx].second] = idx;
        indices[arr[parent].second] = parent;
        idx = parent;
        parent = idx/2;
    }
}
void MinHeap::DownwardHeapify(int n){
    int idx = 1;
    int c_idx = idx;
    while(true){
        int left = idx*2;
        int right = idx*2 + 1;
        if(left < end && arr[c_idx].first > arr[left].first) c_idx = left;
        if(right < end && arr[c_idx].first > arr[right].first) c_idx = right;
        if(c_idx != idx){
            swap(arr[c_idx], arr[idx]);
            indices[arr[c_idx].second] = c_idx;
            indices[arr[idx].second] = idx;
            idx = c_idx;
        } else break;
    }
}

void MaxHeap::UpwardHeapify(int n){
    int idx = n;
    int parent = idx/2;
    while(parent!=0 && arr[idx].first>arr[parent].first){
        swap(arr[idx], arr[parent]);
        indices[arr[idx].second] = idx;
        indices[arr[parent].second] = parent;
        idx = parent;
        parent = idx/2;
    }
}
void MaxHeap::DownwardHeapify(int n){
    int idx = 1;
    int c_idx = idx;
    while(true){
        int left = idx*2;
        int right = idx*2 + 1;
        if(left < end && arr[c_idx].first < arr[left].first) c_idx = left;
        if(right < end && arr[c_idx].first < arr[right].first) c_idx = right;
        if(c_idx != idx){
            swap(arr[c_idx], arr[idx]);
            indices[arr[c_idx].second] = c_idx;
            indices[arr[idx].second] = idx;
            idx = c_idx;
        } else break;
    }
}

int main(){
    std::ios::sync_with_stdio(false);
    std::cin.tie(NULL);
    std::cout.tie(NULL);
    int T;
    std::cin >> T;
    while(T--){
        int k;
        std::cin >> k;
        MinHeap v_min;
        MaxHeap v_max;
        while(k--){
            char command;
            int arg;
            std::cin >> command >> arg;
            if(command == 'I'){
                v_min.append(arg);
                v_max.append(arg);
            } else if(command == 'D') {
                if(arg<0){
                    int ti = v_min.pop();
                    if(ti) {
                        v_max.del(ti);
                    }
                }else{
                    int ti = v_max.pop();
                    if(ti) {
                        v_min.del(ti);
                    }
                }
            }
        }
        if(v_min.end==1 or v_max.end==1) std::cout << "EMPTY" << std::endl;
        else std::cout << v_max.arr[1].first << " " << v_min.arr[1].first << std::endl;
    }
}
```
