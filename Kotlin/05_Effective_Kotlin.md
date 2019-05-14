# Effective-Kotlin
> https://medium.com/@appmattus/effective-kotlin-31215a6cf847 를 바탕으로 작성

### 지난번에 이어서 데이터바인딩에 관해서.
데이터 바인딩을 사용 하기 위해서는
```Kotlin
apply plugin: 'com.adroid.application'
apply plugin: 'kotlin-android'
apply plugin: 'kotlin-android-extensions'
apply plugin: 'kotlin-kapt'

android {
    compileSdkVersion rootProject.compileSdkVersion
    
    dataBinding {   // 이 부분!
        enabled true
    }
```
이렇게 데이터 바인딩 인에이블드 해주면 되고
```Kotlin
    dependencies {
        // implementation "..." 
        // ...
        kapt "com.android.databinding:compiler:$rootProject.dataBindingVersion"
        // ...
    }
```
밑에 데이터바인딩 컴파일러를 추가해 주면 된다.

데이터바인딩이 뷰바인딩의 역할만 하는 것은 아니다.

어떤 자료에서는 findViewByID를 대신할 수 있도록 사용할 수 있다라고 되어 있지고 맞긴 하지만

데이터바인딩의 역할은 안드로이드 아키텍쳐에서 nvm을 하기위한 도구이다.

데이터바인딩을 단순히 사용하기에는 양이 너무 많다.

제대로 활용하기 위해서는 ViewModel이라는 패턴을 이해하고 적용하는 것이 가장 좋다.

```Java
public class SearchViewHolder extends BaseViewHolder<RepsitoriesItme, SearchAdapterViewModel> implements { // ...
    private ItemRepositoryViewBinding binding;
    
    public SearchViewHolder(@NotNull ViewGroup parent) {
        DataBindingUtil.
        this(DataBindingUtil.inflate(LaytoutInflater.from(parent.getContext()), R.layout.item_reository_view // ...
    }
}
```
위는 뷰홀더에 적용을 시켜놓은것 이다.

데이터바인딩을 코드에서 처럼 인플레이트 하고 적용하는 방법이 있고.

또 하나 바로 뷰 크리에이트를 해주고 적용해줄수 있고,

아니면 바로 셋컨텐트뷰를 해주고 레이아웃을 지정해 줄 수 있다.

구글 소스 예제 : https://github.com/googlesamples/android-architecture-components/tree/master/GithubBrowserSample/app/src/main/java/com/android/example/github

액티비티에서는 단순하게 구현

https://github.com/googlesamples/android-architecture-components/blob/master/GithubBrowserSample/app/src/main/java/com/android/example/github/MainActivity.kt



프래그먼트에서 코틀린을 적용 잘 해놓음.

~~https://github.com/googlesamples/android-architecture-components/blob/master/GithubBrowserSample/app/src/androidTest/java/com/android/example/github/ui/user/UserFragmentTest.kt~~

~~코드가 다름~~

샘플 자체가 데이터 바인딩을 위한 샘플

인플래이트를 걸어서 초기화 시키고

콜백도 데이터바인딩으로 받아서 처리하고

onViewCreate() 를 사용하는것이 일반적이지만 요즘에는 onActivityCreated() 를 사용하는 추세.

onCreateView() 에 담는 경우, onActivityCreated() 에 담는 경우 두가지가 있는데

화면 변화가 매번 일어나야 하면 onCreateView() 에 하는게 나을 수 있다.

onActivityCreated() 는 뷰페이지 안에 있어도 매번 다시 그린다.

따라서 매번 다시 그리지 않아도 된다면 onCreateView() 가 좋다.

원래라면 onActivityCreated() 는 액티비티가 붙을때만 동작을 해야한다. 

~~리플레이스가 되어서 그런지 모르지만 없을때도 동작하는듯?~~

인플레이트, 바인딩이 끝나고 나면 레이아웃에 바인딩 코드를 담을 수 있다.

item_repository_view.xml
```Xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="~~~" 
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools">
    
    <data>
    
        <variable
            name="viewModel"
            type="tech.thdev.t~~.SearchHolderViewModel" />
    </data>
```

데이터라는 객체 안에 뷰모델과 타입을 강제로 지정해서 사용할 수 있게 되어있다.

이 뷰모델에 다른 뷰모델을 계속 넘겨주게 되어있는데, 특정하는 행동을 지정할 수 있게 되어있다.

텍스트뷰는 텍스트 대로 모으고 이미지는 이미지 대로 모아주는데 이미지는 별도의 어댑터가 하나 더 필요하다.

어노테이션 바인딩 어댑터 (@BindingAdapter) 라는걸 만들어서 이미지 뷰와 스트링을 넘겨주는걸 만들어 주어야한다.

DataBindingAdapter.kt
```Kotlin
package tech.thdev.tacatdemysampleapp.util

import ...
@BindingAdapter("imageFromUrl")
fun imageFromUrl(imageView: ImageView, imageUrl: String) {
    imageView.loadUrl(imageUrl,R.drawable.ic_search)
}
@BindingAdapter("android:visibility")
fun setVisibility(view: View, value: Boolean) {
    view.visibility = View.VISIBLE.takeIf { value } ?: View.GONE
}
```

조건은 레이아웃(아래 Xml)에서 이미지 뷰에서 이렇게 붙여줘야 알아서 URL과 이미지뷰를 던져서 위쪽(kotlin)의 loadUrl() 을 불러준다.
```Xml
<ImageView
    ...
    app:imageFromUrl="@{viewModel.imageUrl}"
    ...
    app:layout_constraintVertical_bias="0.0"
    tools:src="@mipmap/ic_launcher" />
<TextView
    ...
    anroid:text="@{viewModel.getFullName}"
    ...
    app: layout_constrainStart_toEndOf="@+ad/img_user_avater"
    tools:text="Odaym/Tetris" />
```

이 loadUrl() 은 아까 다른곳에 만들어 놓은 확장함수에서 동작을 하도록 만들어 놓음.

데이터바인딩을 적용하면 코드의 양은 줄어들 수 있다.

