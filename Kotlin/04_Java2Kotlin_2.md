# ViewHolder

## ViewHolder의 정의
> 구글에서 정의해주는 간단한 ViewHolder로 RecyclerView.ViewHolder를 상속받습니다.

```Java
// Provide a reference to the views for each data item
// Complex data items may need more than one view per item, and
// you provide access to all the views for a data item in a view holder
public class ViewHolder extends RecyclerView.ViewHolder {
    // each data item is just a string in this case
    public TextView mTextView;
    public ViewHolder(TextView v) {
        super(v);
        mTextView = v;
    }
}
```

### BaseViewHolder.kt
```Kotlin
package tech.thdev.tacademysampleapp.base.adapter.holder

import android.content.Context
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import tech.thdev.tacademysampleapp.base.adapter.viewmodel.BaseAdapterViewModel

@Suppress("UNCHECKED_CAST")
abstract class BaseViewHolder<in ITEM : Any, VIEW_MODEL : BaseAdapterViewModel> @JvmOverloads constructor(
        itemView: View,
        protected val context: Context = itemView.context) : AndroidViewHolder(
        itemView) {

    constructor(
            layoutRes: Int,
            parent: ViewGroup) : this(
            LayoutInflater.from(parent.context).inflate(layoutRes, parent, false), parent.context)

    private lateinit var _viewModel: VIEW_MODEL

    var viewModel: VIEW_MODEL
        get() = _viewModel
        set(value) {
            _viewModel = value
            _viewModel.onInitViewModel()
        }

    fun checkItemAndBindViewHolder(item: Any?) {
        try {
            onBindViewHolder(item as? ITEM)
        } catch (e: Exception) {
            onBindViewHolder(null)
        }
    }

    /**
     * How to use.
     *
     * viewModel.onClick.... or the others use viewMode
     */
    abstract fun VIEW_MODEL.onInitViewModel()

    /**
     * Use viewHolder
     */
    abstract fun onBindViewHolder(item: ITEM?)
}
```

### BaseRecyclerViewAdapter.java
```Java
package tech.thdev.tacademysampleapp.base.adapter;

import android.support.annotation.NonNull;
import android.support.v7.widget.RecyclerView;
import android.view.ViewGroup;

import kotlin.Unit;
import kotlin.jvm.functions.Function0;
import kotlin.jvm.functions.Function1;
import tech.thdev.tacademysampleapp.base.adapter.holder.AndroidViewHolder;
import tech.thdev.tacademysampleapp.base.adapter.holder.BaseViewHolder;
import tech.thdev.tacademysampleapp.base.adapter.viewmodel.BaseAdapterViewModel;

public abstract class BaseRecyclerViewAdapter<VIEW_MODEL extends BaseAdapterViewModel> extends RecyclerView.Adapter<AndroidViewHolder> {

    private VIEW_MODEL viewModel;

    public BaseRecyclerViewAdapter(@NonNull VIEW_MODEL viewModel) {
        super();
        this.viewModel = viewModel;

        init();
    }

    public VIEW_MODEL getViewModel() {
        return viewModel;
    }

    /**
     * ViewModel의 화면 갱신을 초기화
     */
    private void init() {
        viewModel.setNotifyItemChanged(new Function1<Integer, Unit>() {
            @Override
            public Unit invoke(Integer integer) {
                notifyItemChanged(integer);
                return null;
            }
        });

        viewModel.setNotifyDataSetChanged(new Function0<Unit>() {
            @Override
            public Unit invoke() {
                notifyDataSetChanged();
                return null;
            }
        });
    }

    @NonNull
    @Override
    public AndroidViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        AndroidViewHolder viewHolder = createViewHolder(viewType, parent);
        if (viewHolder instanceof BaseViewHolder) {
            ((BaseViewHolder) viewHolder).setViewModel(viewModel);
        }
        return viewHolder;
    }

    protected abstract AndroidViewHolder createViewHolder(int viewType, @NonNull ViewGroup parent);

    @Override
    public void onBindViewHolder(@NonNull AndroidViewHolder holder, int position) {
        if (holder instanceof BaseViewHolder) {
            ((BaseViewHolder) holder).checkItemAndBindViewHolder(viewModel.getAdapterRepository().getItem(position));
        }
    }

    @Override
    public int getItemCount() {
        return viewModel.getAdapterRepository().getItemCount();
    }

    @Override
    public int getItemViewType(int position) {
        return viewModel.getAdapterRepository().getItemViewType(position);
    }
}
```



```Java
    @NonNull
    @Override
    public AndroidViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        AndroidViewHolder viewHolder = createViewHolder(viewType, parent);
        if (viewHolder instanceof BaseViewHolder) {
            ((BaseViewHolder) viewHolder).setViewModel(viewModel);  // 이 부분 setViewModel로 넘겨주는 부분이 약간 다른 부분(?)
        }
        return viewHolder;
    }

    protected abstract AndroidViewHolder createViewHolder(int viewType, @NonNull ViewGroup parent);

    @Override
    public void onBindViewHolder(@NonNull AndroidViewHolder holder, int position) {
        if (holder instanceof BaseViewHolder) {
            ((BaseViewHolder) holder).checkItemAndBindViewHolder(viewModel.getAdapterRepository().getItem(position));
        }
    }
```
여기서 onCreateViewHolder 를 받아서 CreateViewHolder를 새로 만들어서 작성한 코드대로 만들도록 강제로 변환함.

코틀린으로 작성한 코드를 자바로 변환하다 보니 나온 패턴이라서 원래 코틀린 코드랑은 좀 다르게 작성됨.

onBindViewHolder()가 가장 중요함. 여기서 AndroidViewHolder holder 만들어 진 것을 가지고 매반 checkItemAndBindViewHolder() 로 아이템을 넘겨줌.

이렇게 해서 onCreateViewHolder() 가 길어지는 것을 방지함.

onCreateViewHolder() 가 하는 것은 각각의 ViewHolder 들에서 처리 하는것

예를 들면
```Java
    @Override
    public void onBindViewHolder(@Nullable RepositoriesItem repositoriesItem) {
        binding.setViewModel(new SearchHolderViewModel(repositoriesItem, this));
    }
```
onBindViewHolder() 를 호출 하면 여기서는 데이터 바인딩을 사용해서 코드가 보이지 않지만 실제 코드로 가서 보면

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools">

    <data>

        <variable
            name="viewModel"
            type="tech.thdev.tacademysampleapp.view.main.search.adapter.holder.viewmodel.SearchHolderViewModel" />
    </data>

    <android.support.constraint.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="?selectableItemBackground"
        android:onClick="@{() -> viewModel.onClick()}"
        android:paddingStart="10dp"
        android:paddingTop="12dp"
        android:paddingEnd="10dp">

        <ImageView
            android:id="@+id/img_user_avatar"
            android:layout_width="60dp"
            android:layout_height="60dp"
            android:scaleType="centerCrop"
            app:imageFromUrl="@{viewModel.imageUrl}"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintVertical_bias="0.0"
            tools:src="@mipmap/ic_launcher" />

        <TextView
            android:id="@+id/tv_full_name"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginStart="10dp"
            android:text="@{viewModel.getFullName}"
            android:textColor="#0b4cc4"
            android:textSize="16sp"
            android:textStyle="bold"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toEndOf="@+id/img_user_avatar"
            tools:text="Odaym/Tetris" />

        <TextView
            android:id="@+id/tv_description"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginStart="10dp"
            android:layout_marginTop="4dp"
            android:text="@{viewModel.getDescription}"
            android:textColor="#535353"
            android:textSize="13sp"
            android:visibility="@{viewModel.isShowDescription}"
            app:layout_constraintBottom_toTopOf="@+id/tv_language"
            app:layout_constraintStart_toEndOf="@+id/img_user_avatar"
            app:layout_constraintTop_toBottomOf="@+id/tv_full_name"
            tools:text="Tetris game for Android, using Kotlin ajdfkajdkrjkqejrklajsdfklzajdklfjzklcjflkajdlkrfjqlkejtkladjflkqjektlrjakdlfjklq adjfkqjetkljalkdfj" />

        <TextView
            android:id="@+id/tv_language"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginStart="10dp"
            android:layout_marginTop="4dp"
            android:layout_marginBottom="10dp"
            android:text="@{viewModel.getLanguage}"
            android:textColor="#808080"
            android:textSize="12sp"
            android:visibility="@{viewModel.isShowLanguage}"
            app:layout_constraintBottom_toTopOf="@+id/view_divider"
            app:layout_constraintStart_toEndOf="@+id/img_user_avatar"
            app:layout_constraintTop_toBottomOf="@+id/tv_description"
            tools:text="kotlin" />

        <TextView
            android:id="@+id/tv_star_count"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginStart="8dp"
            android:drawableStart="@drawable/ic_star"
            android:drawablePadding="4dp"
            android:text="@{viewModel.getStargazersCount}"
            android:textColor="#808080"
            android:textSize="12sp"
            android:visibility="@{viewModel.isStargazersCount}"
            app:layout_constraintBottom_toBottomOf="@+id/tv_language"
            app:layout_constraintStart_toEndOf="@+id/tv_language"
            app:layout_constraintTop_toTopOf="@+id/tv_language"
            tools:text="10" />

        <TextView
            android:id="@+id/tv_fork_count"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginStart="8dp"
            android:drawableStart="@drawable/ic_code_fork"
            android:drawablePadding="4dp"
            android:text="@{viewModel.getForksCount}"
            android:textColor="#808080"
            android:textSize="12sp"
            android:visibility="@{viewModel.isForksCount}"
            app:layout_constraintBottom_toBottomOf="@+id/tv_star_count"
            app:layout_constraintStart_toEndOf="@+id/tv_star_count"
            app:layout_constraintTop_toTopOf="@+id/tv_star_count"
            tools:text="10" />

        <TextView
            android:id="@+id/tv_license"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginStart="8dp"
            android:drawablePadding="4dp"
            android:text="@{viewModel.getLicense}"
            android:textColor="#808080"
            android:textSize="12sp"
            android:visibility="@{viewModel.isLicenseVisibility}"
            app:layout_constraintBottom_toBottomOf="@+id/tv_fork_count"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintHorizontal_bias="0.0"
            app:layout_constraintStart_toEndOf="@+id/tv_fork_count"
            app:layout_constraintTop_toTopOf="@+id/tv_fork_count"
            tools:text="Apache license 2.0" />

        <View
            android:id="@+id/view_divider"
            android:layout_width="0dp"
            android:layout_height="0.5dp"
            android:background="#cacaca"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent" />
    </android.support.constraint.ConstraintLayout>
</layout>
```

**예시 구성**
~~https://github.com/taehwandev/GithubUserSearch/tree/master/app/src/main/java/tech/thdev/githubusersearch/view/github/adapter 의  구성~~
UserRecyclerAdapter 에서 createViewHolder() 로 생성 

-> UserSectionViewHolder 에서 onBindViewHolder() 로 타이틀 내용 변경

-> UserViewHolder에서 onBindViewHolder() 로 이미지 로딩, 이름 변경 등등 처리.


UserAdapterViewModel.onInitViewModel() 은 Init에서 한번만 초기화를 하고 그 이후에는 각각의 ViewHolder에서 처리함.

따라서 뷰홀더가 아이템을 들고 있지 않고 어댑터에서 아이템을 넘겨주면 화면에 뿌리기만 한다.


다시 맨 위의 코드로 돌아가서 살펴보면 가장 조심해야 할 것이 맨 위의 제너릭 타입이다.

원래 자바에서는 이러한 제너릭 타입이 모두 오브젝트로 초기화 된다.

```Kotlin
@Suppress("UNCHECKED_CAST")
abstract class BaseViewHolder<in ITEM : Any, VIEW_MODEL : BaseAdapterViewModel> @JvmOverloads constructor(
        itemView: View,
        protected val context: Context = itemView.context) : AndroidViewHolder(
        itemView) {
```
이 제너릭을 사용하는곳을 보면
```Java
    @NonNull
    @Override
    public AndroidViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        AndroidViewHolder viewHolder = createViewHolder(viewType, parent);
        if (viewHolder instanceof BaseViewHolder) {
            ((BaseViewHolder) viewHolder).setViewModel(viewModel);
        }
        return viewHolder;
    }
```
이렇게 되어 있는데 원래라면
```Java
    (BaseViewHolder) viewHolder
    를
    (BaseViewHolder<Object, VIEW_MODEL>) viewHolder
```
이렇게 명시해 주어야 한다. 하지만 명시해주지 않은 이유는 자바에서는 명시하지 않은 것을 자동으로 오브젝트로 처리하기 때문이다.
이 소스를 코틀린으로 변환하면 
```Kotlin
    if (viewHolder is BaseViewHolder<*, *>) {
        viewHolder.viewModel = viewModel
    }
```
이런 식으로 제너릭을 아스타로 명시하여 처리한다. 체크를 하고 싶지 않으면 아스타로 찍어주면 된다.

원래라면 이름을 받아주어야 한다. 

코틀린에서는 상속을 받을때 꼭 명시를 해주어야 한다.


@file:JvmName("LifecycleExtensionsUtil")

@file:Suppress("UNCHECKED_CAST")

와 같은 어노테이션이 있는데 아래 것은 타입캐스트를 체크하지 않겠다라는 의미. 예외처리를 무시

### @file:JvmName("LifecycleExtensionsUtil")
LifecycleExtensionsUtil.kt 에서는 클래스를 정의하지 않고 펑션만 두 개 있는 상태 인데 자바에서 보면 스테틱 매서드 이다.

코틀린에서는 클래스 정의가 없어도 되지만 결국 자바에서 알려면 클래스 정의가 있어야 한 묶음이 된다.

코틀린에서 없는것 처럼 만들었지만 코틀린에서 자동으로 펑션명+kt로 클래스명을 자동 정의 한다.

하지만 kt을 붙인 이름으로 사용하고 싶지 않을때 @file:JvmName("") 을 사용 하는데

여기서 ""안에 클래스명을 적어주면 된다. 하지만 이름과 클래스명을 일치시켜줘야하는 특성 때문에 파일이름으로 지정해야한다.



### 유틸
코틀린에는 원래 없던건데 원래 있던것 처럼 만들어서 쓸 수 있는데 이것이 유틸

이것을 확장해서 사용 할 수 있음.

자바에서는 이렇게
```Java
public class SampleUtil {
    public static void startActivity(Context context, String name) {      // 받아오거나 부정해도 된다.
        Intent intent = new Intent (context, MainActivity.class);       // 메인액티비티 클래스를 초기화시키고
        intent.putExtra("key-name", name);      // 키가 네임인 것을 만들어서
        context.startActivity(intent);        // 스타트 액티비티에 넘겨주는 코드
    }
    public static String getDisplaySize() {
        return "";
    }
}
```

위의 코드를 컨버팅 하면
```Kotlin
fun startActivity(context: Context, name: String) {
    val intent = Intent(context, MainActivity::class.java)
    intent.putExtra("key-name", name)
    context.startActivity(intent)
}
```
가 된다. 하지만 코틀린에서는 익스텐션이 제공되므로 이렇게 간단하게 바꿀 수 있다.
```Kotlin
fun Context.startActivity(name: String) {
    val intent = Intent(this, MainActivity::class.java)
    intent.putExtra("key-name", name)
    this.startActivity(intent)
}
```
이것을 코틀린에서 컨텍스트로 원래있던 것을 부르는것 처럼 사용 할 수 있고, 유틸로 만든 컨텍스트는 ADS에서 노란색으로 표시가 되고 기존에 최상위에서 정의되어 있는 것은 하늘색으로 표시가 된다.
이런 유틸은 주로 라이브러리에서 볼 수 있다.

**this와 it의 차이?**
```Kotlin
fun Context.startActivity(name: String) {
    this.startActivity(
        Intent(this, MainActivity::class.java).apply {  // this: Intent 만약 this에서 정의를 하려하면 오류 발생
        putExtra("key-name", name)
    })
    
    val intent: Intent? = Intent(this, MainActivity::class.java)    // let은 널러블 인 경우 사용함
    intent?.let { intent ->    // it: intent 이렇게 it으로 들어오는것은 정의가 가능
        intent.putExtra("key-name", name)
    }
}
```

어플라이는 값이 생성됨과 동시에 초기화하고 싶으면 사용하고

렛은 
```Kotlin
val repository = Repositories(0, false, null)
repository.items?.let{
    // 이 items에서 뭔가 여러가지를 불러올 필요가 있다면 let블록 을 사용하는게 편하고
    // 여기에 직접 명시를 해줄 수 있어서 편하고, 만약 run이라면 this 이므로
    // 이 안에서 바로 forEach{} 를 하거나 size를 체크하거나 get(0)을 하거나 할 수 있는데
    // let일때는 it.forEach{}, it.size, it.get(0)등 명시한다는 것에 차이가 있다.
    // 강사는 let은 item다시 접근 시에만 사용하고 run은 이미 만들어진 UI에 다시 접근할 때 사용하고 있다.
}

repository.items?.forEach{
    // let으로 사용하면 묶음단위로 조금 편하게 쓴다는 의미이지 실제로는 어떤 것을 직접 접근 해도 된다.
}
```

널이 아닌것이 명확하면 느낌표 두개 !!

널러블이다 물음표 하나 ?

자바코드를 컨버팅 할때 널러블인지 널이 아닌지는 컨버터가 판단을 해 주지 않으므로 주의깊게 볼 것.

### SearchFragment.java -> kt로 컨버팅한 코드 손보기
```Java
    private void showChromeTabs(String url) {
        CustomTabsIntent.Builder builder = new CustomTabsIntent.Builder();
        builder.setToolbarColor(getResources().getColor(R.color.colorPrimary));
        builder.setStartAnimations(requirecontext(), 0, 0);
        builder.setExitAnimations(requireContext(), 0, 0);
        CustomTabsIntent customTabsIntent = builder.build();
        customTabsIntent.launchUrl(requireContext(), Uriparse(url));
    }
```

자동 생성 코드
```Kotlin
    private fun showChromeTabs(url: String) {
        val builder = CustomTabsIntent.Builder()
        builder.setToolbarColor(resources.getColor(R.color.colorPrimary))
        builder.setStartAnimations(requirecontext(), 0, 0)
        builder.setExitAnimations(requirecontext(), 0, 0)
        val customTabsIntent = builder.build()
        customTabsIntent.launchUrl(requireContext(), Uri.parse(url))
    }
```

코드 줄이기
```Kotlin
    private fun showChromeTabs(url: String) {
        //val builder = CustomTabsIntent.Builder()
        //builder.setToolbarColor(resources.getColor(R.color.colorPrimary))
        //builder.setStartAnimations(requirecontext(), 0, 0)
        //builder.setExitAnimations(requirecontext(), 0, 0)
        
        val customTabsIntent = builder.build()
        customTabsIntent.launchUrl(requireContext(), Uri.parse(url))
        CustomTabsIntent.Builder().apply { // this: CustomTabsIntent.Builder
            setToolbarColor(resources.getColor(R.color.colorPrimary))
            setStartAnimations(requirecontext(), 0, 0)
            setExitAnimations(requirecontext(), 0, 0)
        }
    }
```
이렇게 줄일 수 있다. 더 하자면
```Kotlin
    private fun showChromeTabs(url: String) {
        CustomTabsIntent.Builder().apply { // this: CustomTabsIntent.Builder
            setToolbarColor(resources.getColor(R.color.colorPrimary))
            setStartAnimations(requirecontext(), 0, 0)
            setExitAnimations(requirecontext(), 0, 0)
        }.build().run { // this: CustomTabsIntent!
            launchUrl(requireContext(), Uri.parse(url))
        } // apply 코드를 초기화 한 후에 run 을 실행을 해라 라는 의미로 사용 한다.
    }
```
이 코드를 자바로 디컴파일 하면 다시 위쪽의 자바코드로 변경이 된다.

처음 보는 사람을 고려 한다면 자동생성코드처럼 따로따로 빼는 편이 나을 수도 있다.


리스너가 하나짜리인 경우에는 Single Abstract Method(SAM)에 의해서 자동으로 람다식으로 바뀌지만

아래처럼 두 개가 있는 경우에는 SAM을 적용시키지 않는다.

여기의 오브젝트는 싱글톤의 오브젝트가 아니고 익명클래스의 오브젝트이다.

```Kotlin
searchView.setOnQueryTextListener(object : SearchView.OnQuertTextListener { // 여기의 오브젝트는 싱글톤 오브젝트와는 다르다.
    override fun onQueryTextSubmit(query: String?): Boolean {
        var searchQuery = query
        if (query == null) {
            searchQuery = ""
        }
        searchViewModel!!.search(searchQuery!!)
        return false
    }
    
    override fun onQueryTextChange(newText: String): Boolean {
        return false
    }
})
```
