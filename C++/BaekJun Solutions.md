acmicpc.net/workbook/view/1946

### 01.hello world 출력
```cpp
#include <iostream>
int main(){
    std::cout << "Hello World!";
    return 0;
}
```
원하면 using namespace std;로 std 생략해보자.
  
### 02.a+b 결과 출력
```cpp
#include <iostream>
int main()
{
    int a=0, b=0;
    std::cin >> a >> b;
    std::cout << a+b ;
    return 0;
}
```  

### 03.a-b 결과 출력
```cpp
#include <iostream>
int main()
{
    int a=0, b=0;
    std::cin >> a >> b;
    std::cout << a-b ;
    return 0;
}
```  

### 04.그대로 출력  
조건
```
입력이 주어진다. 입력은 최대 100줄로 이루어져 있고, 알파벳 소문자, 대문자, 공백, 숫자로만 이루어져 있다.  
각 줄은 100글자를 넘지 않으며, 빈 줄은 주어지지 않는다. 또, 각 줄은 공백으로 시작하지 않고, 공백으로 끝나지 않는다.
```
```cpp
#include <iostream>
#include <string>
int main()
{
    using namespace std;
    string s[100];
    
    for(int i=0; i<100; i++){
        getline(cin, s[i]);
        if (s[i].empty() || s[i]=="" || s[i]=="\n") break;
        if (s[i].size()>100) break;
    }
    
    for(int i=0; i<100; i++){
        cout << s[i] <<endl;
    }
    return 0;
}
```
