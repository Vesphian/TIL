# 03 자바-코틀린 변환

> Java 파일에서 Java 문법으로 작성. Command + option + Shift + k(ctrl + alt + shift + k). 코드 블록 중 필요한 부분만 복사 붙여넣기시 Convert. 

* 단순히 자바 문법을 코틀린 문법으로 1:1 치환 변형이라서 오류가 당연히 날 수 밖에 없다.
* Java API를 꼭 알아야 한다.
* @Nullable에 주의하자! 
* 코틀린은 모든 코드에 널 체크 후에 NullPointerException을 발생시킨다.
* Null safe 방법으로 접근해서 NPE 발생을 줄인다. ? 접근을 통해서 해결 해야 한다.

~~샘플 앱: github.com/taehwandev/TAcademySampleApp 프로젝트 오픈시 오류 발생시 gradle-wrapper.properties에서 버전을 낮추어야 한다.~~

코틀린에서 안드로이드 어노테이션을 사용하려면 
```Kotlin
apply plugin: 'kotlin-kapt'
```
을 추가 해 주어야 한다.

원래 가이드(https://github.com/bumptech/glide) 상에서는annotationProcessor를 추가해야 하지만 kapt를 추가하는 것으로 사용가능하다.
또한 데이터 바인딩을 하려면

```kotlin
android {
    compileSdkVersion rootProject.compileSdkVersion
    
    dataBinding {
        enabled true
    }
}
```
이렇게 안드로이드 밑에 데이터 바인딩 enabled 해주고 
```kotlin
dependencies {
    ...
    kapt "com.android.databinding:compiler:$rootProject.dataBindingVersion"
    ...
}
```
를 추가 해주어야 한다.

