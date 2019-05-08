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

```Kotlin
android {
    compileSdkVersion rootProject.compileSdkVersion
    
    dataBinding {
        enabled true
    }
}
```
이렇게 안드로이드 밑에 데이터 바인딩 enabled 해주고 
```Kotlin
dependencies {
    ...
    kapt "com.android.databinding:compiler:$rootProject.dataBindingVersion"
    ...
}
```
를 추가 해주어야 한다.

자바에서는 findViewById를 해서 접근하게 되는데 코틀린에서는 버터나이프같은것을 사용하지 않고 바로 접근 하게 도와주는 코드가 있다.

```Kotlin
    recycler_view.adapter = adapter!!
```
전제조건이 kotlinx.android.synthetic.main.fragment_default.recycler_view 코틀린에서 제공하는 안드로이드 익스텐션 apply plugin: 'kotlin-android-extensions' 를 이용하면 바로 접근이 된다.

코틀린 익스텐션을 사용하면 HashMap을 생성해주고 이 해쉬맵을 통해서 매번 findViewById를 매번 하지 않게 해준다.
findViewById 한것을 해쉬에 담아주고 해쉬에서 꺼내오기 때문에 캐싱 해놓은것을 가져오는 것이다.(재사용)
해시 클리어 함수까지 자동 생성 해준다.

//리사이클러 뷰를 사용하게 되는데 뷰홀더에서 안드로이드 익스텐션을 사용해야 한다.

AndroidViewHolder.kt
```Kotlin
abstract class AndroidViewHolder(override val containerView: View)
    : RecyclerView.ViewHolder(containerView), LayoutContainer
```

레이아웃 컨테이너를 사용하지 않으면 재사용하지 않고 자바처럼 계속 불러오게 된다.
LayoutContainer는 코틀린에서 자동 생성 한다.

LayoutContainer.kt
```Kotlin
package kotlinx.android.extensions

import android.view.View

puiblic interface LayoutContainer {
    public val containerView: View?
}
```

디컴파일 해보면 캐시를 만들어주냐 안만들어주냐의 차인데 LayoutContainer를 생성해줘야 캐시를 만든다.

#### SearchRepsoitory 변경
원본 자바 코드
```Java
package view.main;

import ...

public class SearchRepository {

    private final GitHubService gitHubService;
    private static SearchRepository(@NotNull GitHubService gitHubService) {
        this.gitHubService = gitHubService;
    }
    
    public static SearchRepository getInstance(@NotNull GitHubService gitHubService) {
        if (instance == null) {
            synchronized (SearchRepository.class) {
                if (instance == null) {
                    instance = new SearchRepository(gitHubService);
                }
            }
        }
        return instance;
    }
    
    private int nowPage = 1;
    
    public Single<List<RepositoriesItem> searchRepositories(String 블라블라) {
        return gitHubService.searchUser(searchKeyword, sort, order, nowPage, perPage).map(blahblah) {
            @Override
            public List<RepositoriesItem> apply(Repositories repositories) throws Exception {
                return repositories.getItems();
            }
        });
        // ...
}
```

컨버팅을 실행 시키면 public static으로 선언 했던 변수들이 companion object{} 블록 안에 선언이 되어있다. 
companion object는 외부에서 접근시에는 static으로 접근 할 수 있게 도와주는 것이다.

자동 컨버팅 결과물
```Kotlin
package view.main

import ...

class SearchRepository private constructor(private val gitHubService: GitHubService) {

    private val nowPage = 1
    
    fun searchRepositories(searchKeyword: String, sort: String, order: String, perPage: Int): Single<List 블라블라~~ {
        return gitHubService.searchUser(searchKeyword, sort, order, nowPage, perPage).map { (_, _, items) -> items }
        
        // 위의 리턴의 람다표현식을
        // return gitHubService.searchUser(~~).map {
        //      it.items
        // } 로 줄여줄 수 있다.
    }
    
    companion object {
        private var instance: SearchRepository? = null

        fun getInstance(gitHubService: GitHubService): SearchRepository {
            if (instance == null) {
                synchronized(SearchRepository::class.java) {
                    if (instance == null) {
                        instance = SearchRepository(gitHubService)
                    }
                }
                return instance // null 값이 아니므로 return!!(느낌표 두개로 강제화)으로 바꿔주어야 한다. 원하지 않는 결과가 나오긴 하지만 일단 리턴 오류는 제거.
            }
        }
    }
}
```

실제로는 호출 할 때는 SearchRepository.**Companion**.getInstance(GithubAPI.getInstance() ...) 형태로 Companion이 중간에 끼어있는 형태로 사용이 된다.
만약 SearchRepository.getInstance를 바로 부르고 싶다면 companion object{} 블록 안에 @JvmStatic이라고 어노테이션을 fun getInstance() 위에 붙여주면 자바에서 처럼 직접 접근이 가능하다.

```Kotlin
    companion object {
        private var instance: SearchRepository? = null

        @JvmStatic
        fun getInstance(gitHubService" GitHubService): SearchRepository {
            return instance ?: synchronized(SearchRepository::class.java) { // instance가 null이면 synchornized 하고
                instance ?: let {   // let 이라는 블록에서 초기화를 시켜주는 것.
                    val create = SearchRepository(githubService)
                    instance = create
                    create  // 람다식이라서 리턴 해줄 필요는 없다.
                }
            }
        }
        // ...
    }
```

여기서 한 번 더 간소화 가능

```Kotlin
    companion object {
        private var instance: SearchRepository? = null

        @JvmStatic
        @Synchronized   // Java 싱크로나이즈드를 코틀린에서는 어노테이션으로
        fun getInstance(gitHubService" GitHubService): SearchRepository {
            return instance ?: synchronized(SearchRepository::class.java) {
                instance ?: SearchRepository(githubService).also {  // also라는 스탠다드 라이브러리를 사용.
                    instance = it   // instance 초기화 됨
                }
            }
        }
        // ...
    }
```

싱글톤
```Kotlin
object SampleObject(val name: String){

}
```
위와 같은 코드는 객체 생성할 때 자동으로 생성 하기 때문에 손으로 만들어 줄 수 없기 때문에 위위의 코드를 사용한다.


```Kotlin
    fun searchRepositories(searchKeyword: String, sort: String, order: String, perPage: Int): Single<List 블라블라~~ {
        return gitHubService.searchUser(searchKeyword, sort, order, nowPage, perPage)
            .map { (_, _, items) -> items }
        
        // 위의 리턴의 람다표현식을
        // return gitHubService.searchUser(~~)
        //     .map {
        //         it.items
        //     } 로 줄여줄 수 있다.
        
        // 혹은 특정 값만 가져오고 싶다면 (Repository에 total_count, incomplete_results, items 3가지가 원래 있다.)
        // return gitHubService.searchUser(~~)
        //     .map { (count, _, items) ->
        //         println("count $count")
        //         items
        //     }
    }
```

### 코드 줄이기
```Java
package network;

import contract.Contract;

public class GithubAPI {

    private GitHubService gitHubService;

    private GithubAPI() {
        gitHubService = RetrofitCreator.createRetrofit(Contract.API, GitHubService.class);
    }

    private static GithubAPI instance;

    public static GithubAPI getInstance() {
        if (instance == null) {
            synchronized (GithubAPI.class) {
                if (instance == null) {
                    instance = new GithubAPI();
                }
            }
        }
        return instance;
    }

    public GitHubService getGithubService() {
        return gitHubService;
    }
}
```

컨버팅 실행 코틀린 코드
```Kotlin
package network

import contract.*

class GithubAPI private constructor() {
    
    val getGithubService: GitHubService
    
    init {
        gitHubService = RetrofitCreator.createRetrofit(Contract.API, GitHubService::class.java)
    }
    
    companion object {
        private var instance: GitHubAPI? = null
        
        fun getInstance(): GithubAPI {
            if (instance == null) {
                synchronized(GithubAPI::class.java) {
                    if (instance == null) {
                        instance = GithubAPI()
                    }
                }
            }
            return instance
        }
    }
}
```


컨버팅 코드 줄이기
```Kotlin
package network

import contract.*

object GithubAPI {  // 오브젝트 활용
    
    val getGithubService: GitHubService
    
    init {
        gitHubService = RetrofitCreator.createRetrofit(API, GitHubService::class.java)
    }
    
    // companion object {      // 오브젝트 안에서 컴패니언 오브젝트 사용 불가. 따라서 생략 가능. 아래는 필요 없어서 생략.
    //     private var instance: GitHubAPI? = null
    //     
    //     fun getInstance(): GithubAPI {
    //         if (instance == null) {
    //             synchronized(GithubAPI::class.java) {
    //                 if (instance == null) {
    //                     instance = GithubAPI()
    //                 }
    //             }
    //         }
    //         return instance
    //     }
    // }
}
```



늦은 초기화(호출시 초기화) 설명

```Kotlin
class Late {
    
    private var a: String = ""      // 전역 변수 선언시 뒤에 디폴트 값을 주지 않으면 오류가 난다.
    private lateinit var name: String   // 이렇게 하면 name은 초기화를 하기 전까지는 접근시 오류 
    
    private val name2: String by lazy { //lazy는 자동으로 동기화를 해준다. 할 필요 없다 생각하면 by lazy(LazyTheadSafetyMode.NONE) 에서 4가지 종류로 옵션을 고를 수 있다. 멀티스레드 아니라면 NONE으로 초기화 권장
        "name"
    }
    
    @Test
    fun test() {
        // if(this::name.isInitialized) {   //this는 생략가능
        //     println("name $name")
        // }
        name = "Name"
        println("name $name")
    }
}

서비스를 싱글톤으로 가지고 있고 늦은 초기화 적용
```Kotlin
package network

import contract.*

object GithubAPI {
    
    val getGithubService: GitHubService
    
    val gitHubService: GitHubService by lazy{
        RetrofitCreator.createRetrofit(API, GitHubService::class.java)
    }
}
```
lazy의 기본 옵션으로 인해 자동 동기화가 일어난 늦은 초기화가 된다.
만약 자바에서 호출 될 것을 고려한다면 @JvmStatic 어노테이션을 변수 선언 위에 적어주면 INSTANCE를 통해서도 가능하고 없이도 가능하지만 이렇게 하면 오브젝트를 사용한 의미가 없어지긴 한다.


진행 되지 않는다면 안드로이드 스튜디오 버전과 데이터바인딩 버전을 일치시켜주어야 하고 코틀린을 최신버전으로 사용해볼 것.
