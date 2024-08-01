---
title: "Baek Joon - s4_4949_parenthesesBalance"
categories: [PS]
tags: [] # TAG names should always be lowercase
---
## 문제 : 
> #### checkVPS문제랑 비슷한데, 괄호의 종류가 2가지로 늘어남. 또한, valid한 두 괄호가 있을 때, 그 사이에 있는 문자열도 valid해야함.

## 풀이1 : #Full_implementation, #Hard_coding
> #### 내 기준으로 매우 까다로웠던 문제.

### idea :
> #### valid한 두 괄호 사이의 문자열이 valid한지 판별하는 로직을 설계하는것이 가장 까다로웠음.
> #### 소괄호와 대괄호가 섞여있을 때, 위 조건을 만족시키는지 판별하기가 어려움.
> #### 소괄호와 대괄호에 각각 서로를 소유할 수 있는 체계를 만들어 오브젝트로 선언함.
> #### 두개의 스택을 만들고 소괄호와 대괄호를 각각 관리하면서 판단...

## Code1 :
```cpp
#include <iostream>
#include <string>
#include <vector>

class Bracket{
public : 
    int this_count;
    int child_count;
    Bracket();
    ~Bracket();
};
Bracket::Bracket():this_count(1), child_count(0) {};
Bracket::~Bracket(){

}

void clearAlloc(std::vector<Bracket*>& storage){
    for(auto element : storage) delete element; 
}

bool checkVPS(const std::string& sen){
    std::vector<Bracket*> small_storage;
    std::vector<Bracket*> big_storage;
    for(char c : sen){
        if(c == '('){
            small_storage.push_back(new Bracket());
            if(!big_storage.empty()) big_storage.back()->child_count++;
        } else if(c == ')'){
            if(small_storage.empty() || small_storage.back()->child_count > 0 ){
                clearAlloc(small_storage);
                return false;
            } else {
                delete small_storage.back();
                small_storage.pop_back();
                if(!big_storage.empty()) big_storage.back()->child_count--;
            }

        } else if(c == '['){
            big_storage.push_back(new Bracket());
            if(!small_storage.empty()) small_storage.back()->child_count++; 
        } else if(c == ']'){
            if(big_storage.empty() || big_storage.back()->child_count > 0){
                clearAlloc(big_storage);
                return false;
            } else{
                delete big_storage.back();
                big_storage.pop_back();
                if(!small_storage.empty()) small_storage.back()->child_count--;
            }
        }
    }
    return small_storage.empty() && big_storage.empty() ? true : false;
}

int main(){
    std::string sentence;
    
    while(true){
        std::getline(std::cin, sentence,'.');
        std::cin.ignore();
        if(sentence.empty()) break;
        std::string result = checkVPS(sentence) ? "yes" : "no";
        std::cout << result << std::endl;
    }
}
```
> #### 뭔가 코드가 비효율적인 느낌.
----------------------------------------------------------------
## 풀이2 : #implementation
> #### 풀이 1은 너무 돌아갔음... 사실 1차원적으로 생각하면 되었던 문제

### idea :
> #### 괄호가 닫힐 때, 가장 근접해있는 오픈 상태의 괄호의 종류가 다르면 invalid.

## Code2 :
```cpp
#include <iostream>
#include <vector>
#include <string>

bool checkVPS(const std::string& sen){
    std::vector<char> storage;
    for(auto c : sen){
        if(c=='(') storage.push_back('(');
        else if(c=='[') storage.push_back('[');
        else if(c==')'){
            if(storage.empty() || storage.back()=='[') return false;
            storage.pop_back();
        }
        else if(c==']'){
            if(storage.empty() || storage.back()=='(') return false;
            storage.pop_back();
        }
    }
    return storage.empty() ? true : false;
}

int main(){
    std::string sentence;

    while(true){
        std::getline(std::cin, sentence, '.');
        std::cin.ignore();
        if(sentence.empty()) break;
        std::string result = checkVPS(sentence) ? "yes" : "no";
        std::cout << result << std::endl;
    }

}
```

