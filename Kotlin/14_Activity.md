# Activity  
* 하나의 앱은 독립된 4대 구성 요소로 구성된다.  
* 액티비티 : 화면을 관리하는 실행단위  
* 서비스 : 백그라운드에서 실행되는 실행단위  
* 브로드 캐스트 리시버 : 문자가 오거나 배터리가 부족할때 등 시스템 상태를 모니터링 하다가 메시지를 전달받아 실행되는 실행단위  
* 컨텐츠 프로바이더 : 저장된 데이터를 다른 앱에서 쓸 수 있도록 제공하기 위한 실행단위

## Intent  
* 안드로이드는 4대 구성요소를 실행하기 위해서는 Intent라는 객체가 필요하다.  
* 안드로이드의 4대 구성요소를 실행하기 위해서는 개발자가 직접 실행하는 것이 아니라 OS에게 실행을 요청하게 된다.  
* 실행 요청을 전달받은 OS는 해당 실행요소를 실행하게 되는데 실행요청 정보를 담는 객체가 Intent이다.

## 액티비티 실행 및 종료  
* startActivity : 지정된 인텐트에 담긴 정보를 토대로 액티비티를 실행한다.  
* finish : 현재 실행되어 있는 액티비티를 종료한다.

### Back Stack  
* 액티비티에서 다른 액티비티를 실행하면 이전 액티티비티는 백스택에 담겨 정지상태가 되고 새로 실행된 액티비티가 활동하게 된다.  
* 새로 실행된 액티비티가 제거되면 백스택에 있던 액티비티가 다시 활동하게 된다.  
말 그대로 스택 형태로 새로운 액티비티가 실행될때마다 이전 액티비티가 스택에 들어가고 새 액티비티가 쌓이는 형태이다.

### 인텐트 필터
* 이 앱을 대표하는 이름을 설정할 때  
* 안드로이드 앱이 실행되면 첫번째 화면이 무엇인지 알려면 매니페스트에 있는 anroid.intent.action.MAIN 이 설정되어있는 액티비티를 메인으로 실행  

```Kotlin
package com.vesphian.runactivity

import android.content.Intent
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import kotlinx.android.synthetic.main.activity_main.*

class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        button.setOnClickListener { view ->
            var intent = Intent(this, SecondActivity::class.java)
        }
    }
}
```
세컨트 액티비티를 지정 한 것이고, 코틀린에서는 객체를 직접 생성해주지 않는다. 자바에서는 SecondActivity.class 라고 하지만 코틀린은 SecondActivity::class.java 이렇게 작성을 한다.  

```Kotlin
package com.vesphian.runactivity

import android.content.Intent
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import kotlinx.android.synthetic.main.activity_main.*

class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        button.setOnClickListener { view ->
            var intent = Intent(this, SecondActivity::class.java)
            startActivity(intent)
        }

        button3.setOnClickListener { view ->
            finish()
        }
    }
}
```

해당 코드의 구성은 세컨드 액티비티에 버튼 하나 놓고 버튼에 피니시 메소드를 할당 해놓았고, 메인 액티비티에서 세컨드 액티비티 호출 버튼 하나, 메인 액티비티 피니시 버튼 하나를 구성 해놓은 코드이다.  
여기서 세컨드 액티비티는 버튼과 텍스트 하나씩 밖에 없으므로 생략했다.

