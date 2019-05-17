예제소스 빌드 중에 다음과 같은 오류가 뜬다면 

``` Failed to find target with hash string 'android-O' ``` 

``` 프로젝트명 > Application > build.gradle ``` 에 가서

```Gradle
android {
    compileSdkVersion "android-27"
    buildToolsVersion "25.0.3"

    defaultConfig {
        minSdkVersion 21
        targetSdkVersion "android-27"
    }
```
에서 compileSdkVersion와 targetSdkVersion를 수정해주자.

---

# 안드로이드 4대 구성 요소

 1. Activity
 
 2. Service
 
 3. Broadcast Receiver
 
 4. Content Provider

### 1. Activity

눈에 보이고 사용자와 상호작용을 하는 것.

### 2. Service

액티비티와 같지만 백그라운드 프로세싱으로 화면이 없다.

### 3. Broadcast Receiver

시스템을 감시하는 목적으로 많이 사용한다. 다른 앱에서 사용하는 부분을 일시적으로 동작 시킬때 사용.

### 4. Content Provider

A라는 앱에 저장한 데이터를 다른 앱에서 사용할 수 있도록 해주는 것.


## 파일트리 설명
### manifests
: 안드로이드 OS에 알려주고자 하는 정보를 작성하는 곳.

### Java > 패키지 > MainActivity.kt
: 메인 파일

### res
: xml, 이미지 이런 자료를 담아놓는 곳

### manifests > AndroidManifest.xml
```Xml
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>

                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
        </activity>
```
안드로이드는 앱을 구성하는 모든 부분을 이 매니페스트에다가 기록을 하게 되어있다. 

안드로이드에 이 앱이 어떤 정보를 가지고 구성되어있는지를 알려주는 역할을 한다고 보면 된다.

이 중에 ```<intent-filter>``` 는 안드로이드가 각각의 구성요소를 구분하기위한 이름이라고 보면 된다.

```<action android:name="android.intent.action.MAIN"/>``` 가 외부에서 이 액티비티를 찾을 수 있는 이름이 된다.

MAIN을 시켜서 첫 화면을 보이게 된다. 이 MAIN 액션이 속해있는 액티비티의 name이 클래스의 이름이 된다.

Java > MainActivity.kt의 객체를 들어가보면 자동으로 onCreate()가 생성이되고 여기에 있는 setContentView(R.layout.activity_main)는 res 폴더의 layout 밑에 activity_main.xml을 보여주게 된다.

이 xml을 수정해서 Text 탭과 Design 탭으로 화면을 구성 하면 된다.

~~버튼 하나 올려 놓을 때  androidx.constraintlayout.widget.ConstraintLayout 를 LinearLayout 로 바꿔주어야 Constraint 오류가 사라진다.~~


## 액티비티 생명 주기
https://developer.android.com/reference/android/app/Activity.html#ActivityLifecycle

![LifeCycle](https://developer.android.com/images/activity_lifecycle.png)

onCreate() > onStart() > ```onResume() > Running > onPause(}``` > onStop() > onDestroy()

onResume(), onPause() : 눈에서 사라질 때
onStart(), onStop(), onRestart() : 프로세스 킬 혹은 종료 할 때?

안드로이드에서 제공하는 로그캣이라는 것은 Log.d() Log.e() 등 여러가지 있는데 이것은 로그캣에 출력해주는 것은 동일하고 색상 차이라고 보면 된다.

이 로그캣 이름을 이용하여 보고싶은 로그만 검색해서 볼 수 있다.

위의 생명주기 함수를 오버라이드 하여 로그를 사용하면 각 함수 동작 여부를 확인 가능 하다.
```Kotlin
Log.d("abcd", "on Create")
```
이런식으로 작성 후 검색은 abcd로 검색하면 해당 로그만 볼 수 있다.


