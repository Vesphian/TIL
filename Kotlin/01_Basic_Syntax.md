# 기본 문법 1

> 코틀린은 Effective Java의 룰을 그대로 따르고 있기 때문에 왜 그렇게 동작하는지에 대한 궁금증은 이펙티브 자바의 책을 찾아보면 알 수 있다.
또한 스칼라 기반의 개발 언어이므로 스칼라가 좀 더 상위 언어이다.

### 코틀린 기본 변수선언
```Kotlin
val variable: Double = 123.5
val variable: Float = 123.5f
    ...
val variable: String = "a"
```
(: 변수형)생략 가능.

단, 널형 변수를 선언 할 경우
```Kotlin
val variable: Nothing? = null
```
라고 선언 해주어야함.

(mutable) Read/Write : var
```Kotlin
var variable = "a"
```
(immutable) Read : val
```Kotlin
val variable = "a"
```
```Kotlin
var <propertyName>[: <PropertyType>] [= <property_initializer>]
     [<getter>]
     [<setter>]
```
--> 괄호를 사용하는 부분은 모두 생략이 가능

### get/set custom
```Kotlin
class Sample<T> {

    var list: List<T> = mutableListOf()
        set(value) {
            if (value.isNotEmpty()) {
                field = value
            }
        }
        get() = field

    val isEmpty: Boolean
        get()  = this.list.isEmpty()
}
```
여기서 field는 자기 자신을 가르킨다.
field에 적용을 해주지 않으면 그냥 받아오고 끝.
여기서 get()이 없어도 자동으로 field값을 리턴 해 줄 것이다.

* 코틀린 변수 널러블 Nullable
모든 변수는 널을 허용하지 않는다.
허용하려면 물음표(?)를 추가해야 한다.
```Kotlin
var variable : String? = "a"
```

* 코틀린 변수 캐스트
as : 값의 casting
```Kotlin
val a: Any? = "ABC"     //--> Any는 자바의 Object 타입이다.
val b: Int? = a as? Int     //--> a가 int 형이면 형변환을 해서 b에 입력
println("Out b $b")   // Out b null
```

is : 값이 맞는지 체크
```Kotlin
val a: Any? = "ABC"
var b: Int? = 0
if (a is Int) {
    b = a as? Int
}
println("Out b $b") // Out b 0
```

as : 값의 casting 할 경우 null return
```Kotlin
val a: Any? = "ABC"
val b: Int? = a as? Int
println("out b $b")  // Out b null
```

Null 나오지 않게 하려면?
```Kotlin
val a: Any? = "ABC"
var b: Int? = a as? Int
if (b== null) {
    b = 0
}
println("out b $b")
```

### Elvis Operator
```Kotlin
val a: Any? = "ABC"
val b: Int? = a as? Int ?: 0
println("out b $b")
```
여기서 ?: 를 나타내는데 if-else의 else를 대신 하는것.

### 코틀린 변수 엘비스 오퍼레이터, !!
```Kotlin
val l: Int = if (b != null) b.length else -1

?: (else)
val l = b?.length ?: -1
```
Other example
```Kotlin
fun foo(node: Node): String? {
    val parent = node.getParent() ?: return null
    val name = name nopde.getName() ?:
        throw IllegalArgumentException("name expected")
        // ...
}
```
정말 null이 필요한 경우
```Kotlin
val l = b!!.length
```

### is와 when을 활용시 if 문을 줄일 수 있다.
```Kotlin
val a: Any? = "ABC"
when (a) {
    is Int -> println(a)
    is String -> println(a)
    else -> println("nothing")
}
// print "ABC"
```

* 안드로이드 스튜디오>툴>코틀린>Kotlin REPL…쇼 코틀린 바이트코드(JVM 위에서 동작하는 코드가 보임) 디컴파일 하면 자바의 코드로 변환됨.
___
### Kotlin Class
```Kotlin
class KotlinSample {
}   // 기본적으로 퍼블릭 무조건 전역. 이 패키지에서만 접근하고 싶으면 방법이 없다!!

class KotlinSample constructor() {
}   // 생성자는 생략 가능 그냥 클래스 명으로 생성자 선언.??

class KotlinSample 
constructor(val a: String) {
}   // 생성자에 전역 변수가 됨
```

1개의 primary constructor를 갖을 수 있고
N개의 secondary constructor는 보조 생성자를 생성해야 변수 여러 개 선언 가능.
여러 개 하려면 메인 생성자를 호출을 해줘야 함.

```Kotlin
    class KotlinSample constructor(val a: String) {
    constructor(a: String, b: String) : this(a) -> 무조건 a라고 생성한 생성자를 호출 해줘야 한다.
    constructor(a: String, b: String, c: String) : this(a, b)
}
```
코틀린은 무조건 프라이머리 생성자가 선언이 되어야 하고 생성자 내에서 전역변수를 선언 할 수 없다.

```Kotlin
Class Sample(val name: String) {
    Init {
        Println(“name $name”)   //--> 이닛 블록을 작성 해줘야 뭘 할 수 있음. 생성자에서.
    }

    Constructor(name: String, name2: String) : this(name) {
        Println(“name $name name $name2”)
    }
}
```

자바에서와 다르게 코틀린에서는 초기 생성자에다 모든 변수를 다 선언하고 세컨더리 생성자에서 필요한것만 사용하면 된다.
대신 세컨더리에서 안쓰는 변수는 초기화.
```Kotlin
class Sample(val name: String, val name2: String, val age: Int) {   //--> val name: String, val name2: String
    init {
        println(“name $name name2 $name2 age $age”)
    }
    constructor(name: String) : this(name, name2, age: 0)   //--> name2, age: 0
    constructor(name: String) : this(name, name2: "")   //--> name2: ""

}
```

코틀린에서는 변수에 초기값을 안준다고 오류가 나지 않는다. 
코틀린에서 코틀린 문법으로 선언한 생성자처럼 자바에서 사용하고 싶다면 @JvmOverloads 라는 어노테이션을 추가해주면 자바파일에서 코틀린처럼 사용가능하게 변한다.

### 정리
* 1개의 프라이머리 생성자
* 세컨더리 생성자는 여러 개로 선언 가능.
* 세컨더리에서는 프라이머리를 호출 해야한다.
* 프라이머리 생성자는 이닛init{} 블록을 제공한다.
* 프라이머리 생성자와 함께오는 변수는 전역변수이다.
* 디폴트 값을 함께 가질 수 있고, 여기서 정의한 생성자를 자바에서 부르려면 어노테이션을 사용해야 한다.

데이터 클래스를 쓰는건 외부에서 오는 데이터를 안전하게 처리하기 위해서 헤쉬코드, 이퀄스, 투스트링을 자동으로 만들어 준다.
보안상의 문제로 내부에서 정해놓지 않으면 외부에서 변경이 가능한 문제가 있다. DAO, DTO, 트랜스퍼 오브젝트등 지칭하는 단어는 많다.
