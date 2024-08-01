---
title: "Baek Joon - s2_1541_missingParenthesis"
categories: [PS]
tags: [] # TAG names should always be lowercase
---
## 문제 : 
> #### 괄호를 적절히 쳐서 식의 값을 최소로 만드는 프로그램을 작성하시오.

## 풀이 : #implementation, #math
> #### just 구현

### IDEA :
> #### 처음 `-`부호가 나타나고, 그 이후에 나타나는 `-`부호는 `+`로 바꿔준다.
> #### 적절히 구현하자

## Code :
```cpp
#include <iostream>
#include <string>
#include <algorithm>
#include <vector>
#include <sstream>

std::vector<std::string> splitString(const std::string& str, char delimiter) {
    std::vector<std::string> tokens;
    std::stringstream ss(str);
    std::string token;

    while (std::getline(ss, token, delimiter)) {
        tokens.push_back(token);
    }
    return tokens;
}

void printVector(const std::vector<std::string> V){
    for(const auto& element : V){
        std::cout << element << " ";
    }
}

int main(){
    std::string eqn;
    std::cin >> eqn;
    size_t pos = eqn.find('-');
    int occurrences = 0;
    bool status = false;
    if(pos != std::string::npos){
        status = true;
        occurrences = std::count(eqn.begin(), eqn.begin()+pos, '+');
        //occurrences+1 = number of front part
        eqn.replace(pos, 1, "+");
        while(eqn.find('-') != std::string::npos){
            pos = eqn.find('-');
            eqn.replace(pos, 1, "+");
        }
    }
    std::vector<std::string> tokens = splitString(eqn, '+');
    int ans = 0;
    if(status){
        for(int i=0; i<occurrences+1; i++){
            ans += std::stoi(tokens[i]);
        }
        for(int i=occurrences+1; i<tokens.size(); i++){
            ans -= std::stoi(tokens[i]);
        }
    } else{
        for(const auto& element : tokens){
            ans += std::stoi(element);
        }
    }
    std::cout << ans << std::endl;
}
```
