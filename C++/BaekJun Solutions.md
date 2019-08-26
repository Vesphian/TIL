https://acmicpc.net/workbook/view/1946

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
using namespace std;

int main()
{
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
잊었던 파이프, getline함수  



### 05.그대로 출력 2 
조건  
```
입력이 주어진다. 입력은 최대 100줄로 이루어져 있고, 알파벳 소문자, 대문자, 공백, 숫자로만 이루어져 있다.  
줄은 100글자를 넘지 않으며, 빈 줄이 주어질 수도 있고, 각 줄의 앞 뒤에 공백이 있을 수도 있다.  
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
        if (s[i]=="\n") break;
        if (s[i].size()>100) break;
    }
    
    for(int i=0; i<100; i++){
        cout << s[i] <<endl;
    }
    return 0;
}
```
오히려 조건이 간단해졌다. 입력 탈출 조건이 뭔가 많이 허전한 느낌이지만 정답 처리 해주니까 넘어간다.

### 06.사칙연산  
조건  
```
문제
두 자연수 A와 B가 주어진다. 이때, A+B, A-B, A*B, A/B(몫), A%B(나머지)를 출력하는 프로그램을 작성하시오. 

입력
두 자연수 A와 B가 주어진다. (1 ≤ A, B ≤ 10,000)
추가 사항
```
```cpp
#include <iostream>
using namespace std;
int main()
{
    int a, b;
    cin >> a >> b;
    cout<< a+b << endl << a-b << endl << a*b << endl << a/b << endl << a%b << endl;
    return 0;
}
```

07.나머지는 패스  

### 08.구구단  
예제출력
```
예제 출력 1 
2 * 1 = 2
2 * 2 = 4
2 * 3 = 6
2 * 4 = 8
2 * 5 = 10
2 * 6 = 12
2 * 7 = 14
2 * 8 = 16
2 * 9 = 18
```

```cpp
#include <iostream>
using namespace std;

int main()
{   
    int n;
    cin >> n;
    for (int i=1; i<=9; i++){
        if (n<1 || n>9) break;
        cout << n << " * " << i << " = " << n*i << "\n";
    }
    return 0;
}
```
인라인 계산이랑 문자열 출력을 섞을때 맨 뒤에 endl로 처리하면 오류가 발생함. 왜지??   
~~원래는 std::endl~~  

namespace에 대한 추가 참고사항 [[출처]](https://boxbop.tistory.com/51) 
```
 using namespace std;

라고 작성하면 어떻게 될까요? 이것은 이름공간 std안에 존재하는 이름들은 그냥 참조하겠다고 선언되어 있는 것 입니다.  
따라서 이후부터는 범위지정 연산 없이 접근이 가능하게 되는 것이죠.  
그러나 이와 같은 선언은 나중에 충돌을 일으킬 수 있는 원인이 될 수 있어서  
using std::cout 와 같이 조금은 번거로워도 일일이 작성해주는 편이 좀 더 좋습니다.

int value = 100; // 전연변수

int main(void)
{
   int value = 100;  //지역변수
   ::value += 1;  //전역변수
   return 0;
 }

::value += 1; 에서 범위 지정 연산자를 사용해서 전역변수에 접근을 하고 있습니다.  
그러나 이름공간이 따로 존재하지 않는데요 이는 전역 변수로 선언되어있는 변수 value에 접근하라는 뜻이 됩니다.  
때문에 범위 지정 연선자는 전역 변수에 접근하기 위한 용도로도 사용이 가능합니다.
```
