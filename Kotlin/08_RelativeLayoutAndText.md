## Relative Layout
> * Relative Layout 안에 들어있는 뷰들 간의 관계를 설정하여 배치하는 레이아웃이다.
> * Relative Layout은 별다른 속성이 없고 여기에 배치된 뷰들에게 속성이 추가된다.

### 부모과의 관계
* layout_alignParentTop : 뷰의 상단을 부모의 상단에
* layout_alignParentBottom : 하단을 하단에
* layout_alignParentLeft : 좌측을 좌측에
* layout_alignParentRight : 우측을 우측에

디자인에서 자동으로 드래그로 변경하면 알아서 체크가 변경 된다.
    
* layout_centerHorizontal : 가로 중앙을 가로 중앙에
* layout_centerVertical : 세로 중앙을 세로 중앙에
* layout_centerParent : 뷰의 중앙을 부모의 중앙에

보조선이 생길 때 배치하면 체크 변경이 이루어진다.

### 다른 뷰와의 관계
다른 뷰의 ID 값에 의해 설정

* align_top : 뷰의 상단을 지정된 뷰의 상단에 맞춘다.
* align_bottom : 하단을 하단에
* align_left : 좌측을 좌측에
* align_right : 우측을 지정된 뷰의 우측에 맞춘다.

* below : 지정된 뷰 하단에 위치한다.
* above : 지정된 뷰 상단에 위치한다.
* toRightOf : 우측에 위치
* toLeftOf : 좌측에 위치
* baseline : 베이스라인에 맞춘다.

# Text View

> * text : 문자열 설정
> * textAppearance : 미리 제공되는 테마를 설정

동시에 여러개를 설정 하고 싶다면 Component Tree에서 Shift클릭으로 여러개 선택후 적용.

변수 선언 시 
```Kotlin
var text2:TextView? = null
```
변수명 text2에 TextView 클래스로부터 만든 객체 주소값을 담을 참조변수를 선언

```Kotlin
text2 = findViewById<TextView>(R.id.textView2)
text2?.text = "문자열2"
```
아이디가 textView2 텍스트뷰의 주소값에 text라는 프로퍼티의 값을 **문자열2** 라고 설정 하는것.

```Kotlin
text2 = findViewById<TextView>(R.id.textView2)
text2?.setText("문자열10")
```
이렇게 하면 위의 코드와 같게 문자열 설정을 할 수 있다.
하지만 위의 text2?.text = "" 코드는 코틀린에서 자동으로 setText 함수를 호출하는 것이다.

저 위의 findViewById의 주소값 받아오는 작업을 코틀린에서 사실 사용하지 않는다.

```Kotlin
textView.text = "문자열3"
```
코틀린에서는 해당 클래스명과 동일 변수를 자동으로 선언하고 주소값을 넣어놓기 때문에

그냥 단순히 이렇게만 해도 편리하게 값을 설정 할 수 있다. 

