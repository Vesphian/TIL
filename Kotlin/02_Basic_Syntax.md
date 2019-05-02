# 기본 문법 2

## Kotlin class 상속

### Java

 Default class 
- class, function은 재정의 가능(non final)
    
 Abstract 
- Extends로 상속 구현 가능
    
 Interface 
- Implements로 상속을 구현 가능


### Kotlin

 Default class
- Class, Function은  final 재정의 불가
- 재정의를 하려면 open 키워드를 추가해야 함
    
 상속의 정의는 콜론( : )으로 한다.
 
 다중 상속 정의는 콤마( , )로 한다.
 
 Abstract과 interface 상속 순서가 변경되어도 문제 없다.

```Kotlin
Interface InterfaceKotlineSample {
    fun init()
}
Abstract class AbstractKotlinSample{
    abstract fun init()
}
Class KotlinSample :
    InterfaceKotlinSample, AbstractKotlinSample() {     // <-- 둘의 순서가 바뀌어도 무관,
    override fun init() {
    }
}
```


## Interface

선언가능, default 정의 가능

변수도 선언만 가능(상속에서 구현해야 함)
```Kotlin
Interface InterfaceKotlinSample {
    fun init() {
    }
    fun out()
}
```
변수를 Java처럼 사용하려고 하면 오류 발생.
```Kotlin
val type = 0 // <-- 실제로는 변수가 아니라 함수로 만들어지기 때문에 오류 발생.
```
디컴파일 해보면
```Java
public interface InterfaceKotlinSample {
    int getType();
}
```



### interface – Inheritance
코틀린에서 값이 변하지 않는 **파이널 선언**을 하고 싶다면 --> 처럼 해주어야 한다.
```Kotlin
interface InterfaceKotlineSample {

    val type: Int
    
    fun init() { }
    fun out()
}
```
이 코드를 -->
```Kotlin
interface InterfaceKotlineSample {
    companion object {
        const val type: Int = 0
    }
    fun init() { }
    fun out()
}
```
이렇게

상속시:
```Kotlin
class inheritance :
    InterfaceKotlinSample {
        override val type: Int    // --> 파이널 선언 시 사라진다.
        get() = 0       // --> 파이널 선언 시 사라진다.
    override fun out() {
    }
}
```

--> 파이널 선언 시 아래 처럼 사라진다.
```Kotlin
class inheritance :
    InterfaceKotlinSample {
        // 이 부분.
    override fun out() {
    }
}
```


### 다중상속
```Kotlin
interface interfaceKotlinSample {
    fun init() {
        println("InterfaceKotlinSample – init")
    }
}

abstract class AbstratKotlinSample {

    open fun init() {       // open을 붙여주어야 override 할 수 있다. abstract fun init()로 구현하면 super.init()은 interface의 init()을 호출한다.
        println("AbstractKotlinSample - Init")
    }
}

class KotlinSample : InterfaceKotlinSample, AbstractKotlinSample() {
    override fun init() {
        super.init()
    }
}
```
실행시 
```Many supertypes available, please specify the one you mean in angle brackets, e.g. 'super<Foo>'```에러 발생

실제로 인터페이스와 추상 클래스의 init을 모두 부를 수 있기 때문이다.

따라서
```Kotlin
class KotlinSample : InterfaceKotlinSample, AbstractKotlinSample() {
    override fun init() {
        super<InterfaceKotlinSample>.init()
        super<AbstractKotlinSample>.init()
    }
}
```
이렇게 접근해야 한다.


### Java
- 자바에서는 인터페이스 init은 버려진다. 둘 다 쓰고 싶다면 이름을 다르게 해야한다.

### Kotlin
- interface default 정의
- abstract default 정의
- 상속에서 super.init()을 골라야함 super<GenericType>.init()



## Kotlin Data class

자바에서 변수 선언은 클래스 밑에 그냥 선언 후 캡슐화를 위해 getter, setter를 만들어 주어야 했고
equals()와 hashCode()를 선언해주어야 한다.


```안드로이드 스튜디오 3.0 이상부터는 자동으로 코틀린 자바 컨버트 기능이 내장되어 있기 때문에 Code>Convert Java File to Kotlin File을 할 수가 있다. 혹은 액션에 직접 지정 하거나 단축키로 한번에 가능.```

```Kotlin
package

import com.google.gson.annotations.SerializedName

class UserItem(@field:SerializedName(value: "login")
                val login: String, @field:SerializedName(value: "id")
                val id: Int, @field:SerializedName(value: "avatar_url")
                val avatarUrl: String)
```
위의 코드와 아래의 코드의 차이점은 클래스 앞에 **data**가 붙은것 밖에 없는데 이 data 클래스로 선언되면

**내부 디컴파일**시에 component를 자동으로 생성해주고 객체 카피를 위한 copy() 메소드와 copy$default() 메소드,

toString() 메소드도 자동으로 추가 해주고 hashCode(), equals() 메소드 까지 만들어준다.
```Kotlin
package

import com.google.gson.annotations.SerializedName

data class UserItem(@field:SerializedName(value: "login")
                val login: String, @field:SerializedName(value: "id")
                val id: Int, @field:SerializedName(value: "avatar_url")
                val avatarUrl: String)
```



## Kotlin Sealed class
> 자바의 다형성이라고 보면됨. 부모객체를 통해 하위 객체를 접근하는 방법과 유사하고 다형성을 쉽게 만들어 놓은 것이다.

```Kotlin
sealed class Expr
data class Const(val number: Double) : Expr()
data class Sum(val e1: Expr, val e2: Expr) : Expr()
object NotANumber : Expr()

fun eval(expr: Expr): Double = when(expr) {
    is Const -> expr.number
    is Sum -> eval(expr.e1) + eval(expr.e2)
    NotANumber -> Double.NaN
    // the 'else' clause is not required because we've covered all the cases
}

@Test
fun test() {
    println(eval(Sum(Const(0.0), Const(1.0))))
}
```

원래 sealed class를 구현할 때 else가 필요하지만 여기서 쓰지 않았는데, 상속구현이 되었다는 전제 하에 코드가를 짰기 때문이다.
만약 else의 조건에 걸리게 되는 예외 상황이라면 애초에 eval() 메소드에 접근 자체가 불가능하다.



## Kotlin singleton
### Java
```Java
public class Eager {
    private static Eager instance = new Eager();
    
    private Eager() {}
    
    public Eager getInstance() {
        return instance;
    }
}
```

### Kotlin
```Kotlin
object Eager
```
코틀린에서 오브젝트로 선언만 하면 위와 같은 자바코드가 나온다.



## Kotlin Lambdas
익명 클래스 혹은 무명 클래스 라고도 한다. 자바에서는 Java 8 에서 부터 람다식을 제공한다.

코틀린에서 람다를 표현하는 방법은 변수의 유무, 리턴을 생략하는 등 여러가지 방법이 있다.
```Kotlin
btn.setOnTouchListener { view: View?, motionEvent: MotionEvent? -> 
    Log.i("SampleActivity", motionEvent ${motionEvent?.action}")
    return@setOnTouchListener false // 리턴은 명시하기 위해 넣음 원래는 3번째 코드와 같이 리턴이 없음.
}
```

코틀린에서는 파라메터 타입을 생략 가능
```Kotlin
btn.setOnTouchListener { view, motionEvent -> 
    Log.i("SampleActivity", motionEvent ${motionEvent?.action}")
    return@setOnTouchListener false
}
```

return 명시도 하지 않을 수 있음
```Kotlin
btn.setOnTouchListener { view, motionEvent -> 
    Log.i("SampleActivity", motionEvent ${motionEvent?.action}")
    false   // false를 적어주지 않으면 Log가 Int를 반환하는데 Int를 리턴할 수 없으므로 에러 발생.
}
```

파라메터가 1개이면 it 키워드를 사용할 수 있다.
```Kotlin
btn.setOnClickListener {
    Log.d("SampleActivity", "view $it")
    // 온클릭 리스너 이기 때문에 리턴이 없음
}
```



## Higher-Order Functions (고차 함수)
> 온클릭 리스너는 특이 케이스로 SAM(Single Abstract Method)라는 경우에만 코틀린에서 자동 생성해주지만
그것과 똑같이 구현하기 위해서는 Higher-Order Functions을 구현해 주어야 한다

### Java
- Java에서 interface 정의를 하고, interface(객체)를 넘기는 식으로 한다.
- 대표적인 예로 setOnClickListener
```Java
public interface OnClickListener {
    void onClick(View v);
}

public void setOnClickListener(@Nullable OnClickListener l) {
    mOnClickListener = l;
}

setOnClickListener(new OnClickListener()
{
    @Override
    public void onClick(View v) {
        // ...
    }
});
```



### Kotlin
- 함수를 파라메터로 전달, 함수 return
- lambda를 이용하여 축약 형태로 제공, 리턴 결과도 lambda로 사용 가능
- 변수로 함수를 가질 수 있다
```Kotlin
private var onClick: ((view: View) -> Unit)? = null
                                    // 자바의 void 대신 Unit이라는 키워드를 사용함. 동일한 것.
fun setOnClick(onClick: (view: View) -> Unit) {
    this.onClick = onClick
}
```
사용
```Kotlin
setOnClick {
    // ...
}
```


**실제 코드**
```Kotlin
package

import org.junit.Test
class UnitTest {
    var onclick: (position: Int) -> Unit = { position -> // it: int 잇을 사용해서 받아도 됨
        println("position $position")
    }
    
    @Test
    fun test() {    // 함수 리플렉션을 하게 되면 정확히 어떤 함수를 부르는지 모르는데 안다면 
        onclick.invoke(position: 0)     //인보크를 사용 할 수 있다.
        onclick(position: 0)            //디컴파일에서 보면 자바코드로는 모두 invoke를 자동으로 해준다.
    }
}
```

```Kotlin
package

import org.junit.Test
class UnitTest {
    var onclick: (position: Int, a: String = "") -> Unit = { position, a-> 
                                        //메소드를 갖지만 디폴트 값을 가질수는 없다.
        println("position $position")
    }
}
```
파라메터는 22개 까지 대응을 시킬 수 있다.

* 응용코드
```Kotlin
fun higherOrder(body: (Int, Int) -> Int) = body(20, 10)  // body라는게 호출이 되면 자동으로 20과 10을 리턴
fun sum(a: Int, b: Int) = a + b
fun minus(a: Int, b: Int) = a - b
fun multiply(a: Int, b: Int) = a * b
fun division(a: Int, b: Int) = a / b

@Test
fun test() {
    println(higherOrder(::sum)) // 30
    println(higherOrder(::minus)) // 10
    println(higherOrder(::multiply)) // 200
    println(higherOrder(::division)) // 2
```
위 코드에서 higherOrder()의 입력되야할 값과 내부 함수(여기서는 sum(), minus()등)의 변수가 같으면 {}로 연결해서 호출 하는 것이 아니라 콜론 두 개로 엮어줄 수 있다.

