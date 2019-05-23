# 안드로이드 뷰의 개념

## 뷰
> 안드로이드에서 눈에 보이는 모든 요소를 View라고 부르며 개발자가 배치한 모든 View들은 Class로 제공되는데 View라는 클래스를 상속받고 있다. View 클래스는 모든 UI요소들의 부모클래스로써 위젯과 레이아웃으로 나뉜다.


## 레이아웃
> **컨테이너**, 뷰 그룹이라고 부르기도 하며 다른 뷰들을 포함 하고(컨테이너) 내부의 뷰를 통합 관리하고(뷰 그룹) 내부의 뷰들이 배치되는 모양을 결정(**레이아웃**)한다.

> 안드로이드는 뷰가 배치될 위치를 결정하지 않고 형태를 결정한다. 개발자가 형태를 결정하면 안드로이드 OS에서 스스로 최적화된 사이즈와 위치를 결정한다.


## 위젯
> 문자열 입력, 문자열 출력 등 어떤 기능을 가지고 있고 사용자와 상호작용을 하는 뷰들을 통칭해서 위젯이라고 부른다.

---

### 뷰의 주요 속성
* ID : xml이나 코드에서 뷰를 지칭하기 위해 사용하는 속성
* layout_width : 뷰의 가로 길이
* layout_height : 뷰의 세로 길이
    - match_parent : 벗어나지 않는 최대 사이즈
    - wrap_content : 자신을 온전히 보여 줄 수 있는 최소의 사이즈
    - 직접 숫자값을 사용하게 되면 액정 사이즈에 의해 짤리거나 작게 보이게 되므로 지양한다.
    
* margin : 뷰의 외부 여백
* padding : 뷰의 내부 여백
    - 디자인의 속성 탭에 All Attributes 아래에 layout_margin, layout_padding 으로 정의 되어 있다.
    
* layout_gravity : 뷰의 위치 정렬
    - layout_gravity : 리니어 레이아웃을 사용하면 아래 정렬이 불가능하다.
* gravity : 뷰의 내부 정렬
* background : 뷰의 배경 지정
    - #FFFFFF 형태로 색상을 지정 할 수 있다.

---

### 레이아웃
> Linear Layout
* 좌에서 우, 위에서 아래 방향으로 뷰를 배치
* orientation : 뷰가 배치될 방향
* weight : 리니어 레이아웃에 배치된 뷰의 속성으로 배치 후 남은 공간을 할당 받을 비율을 설정한다.

~~ConstraintLayout은 한번 만든 뷰를 가지고 다양한 디바이스에 적용하기 위한 것으로 나중에 리소스 할 때 다룸.~~

weight 를 설정 하면 남은 공간을 설정 한 수치만큼의 등분으로 나누어서 크기를 갖는다.

예를 들어 버튼 총 4개를 넣을 수 있는 공간에 버튼 두 개를 wrap_content로 만들고 그 중에 한 버튼의 weight를 1로 설정 하면 남은 공간 2를 1등분 하여 차지한다. 그래서 총 3 크기의 버튼 하나와 1크기 버튼 하나 이렇게 두개가 된다.

만약 두 버튼 모두 weight를 1을 가지면 둘 다 균등하게 남은공간을 1:1로 가져간다.

하나가 2 나머지가 1이라면 남은 공간을 3등분 하여 하나가 2만큼 나머지가 1만큼을 가져간다. 

**절대 2:1 비율의 크기로 배치가 되는 것이 아니다.**


orientation은 설정 하지 않으면 기본적으로 horizontal 이라고 보면 된다.

Linear Layout은 방향성을 가지고 뷰를 배치하는 Layout이다.