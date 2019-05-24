# 쓰레드

* 쓰레드는 여러 처리를 비동기적으로 처리하기 위해 사용함  
* 안드로이드 비동기 처리 이외에도 네트워크에 관련된 코드는 전부 쓰레드를 써야한다.

## ANR
> * 안드로이드는 액티비티의 코드를 처리하기 위해서 쓰레드를 발생 시킨다. 여기서 발생되는 쓰레드를 Main Thread 라고 하며 UI 쓰레드 라고도 한다.
> * 메인 쓰레드가 현재 작업을 하지 않을 때만 화면작업이 가능하며 메인쓰레드가 바쁠 때 화면 작업이나 터치가 발생하면 ANR (Application Not Respond)가 발생한다.


하나는 쓰레드를 작성해서 현재시간(밀리초)을 출력하고 하나는 버튼을 누를때 앱 텍스트뷰에 출력.

```Kotlin
package com.vesphian.thread

import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.os.SystemClock
import android.util.Log
import kotlinx.android.synthetic.main.activity_main.*

class MainActivity : AppCompatActivity() {

    var isRunning = false

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        button.setOnClickListener { view ->
            var now = System.currentTimeMillis()
            textView.text = "버튼 클릭 : ${now}"
        }

        /*      // 이렇게 작성하면 메인 쓰레드가 이 반복문을 처리하느라 화면이 그려지지 않게 된다.
        while(true) {
            var now = System.currentTimeMillis()
            textView2.text = "무한 루프 : ${now}"
        }
        */

        isRunning = true
        var thread = ThreadClass1()
        thread.start()
    }

    inner class ThreadClass1 : Thread() {
        override fun run() {
            while (isRunning) {  // 변수를 true로 짜면 앱이 종료되어도 쓰레드가 종료되지 않는다.
                SystemClock.sleep(100)
                var now = System.currentTimeMillis()
                Log.d("test1", "쓰레드: ${now}")
                
                textView2.text = "쓰레드 : ${now}"
            }
        }
    }

    override fun onDestroy() {  // 앱 종료시 쓰레드 종료.
        super.onDestroy()
        isRunning = false
    }
}
```

이런 식으로 개발자가 발생 시킨 쓰레드로 화면을 변경 하는것은  
오레오부터 가능하기 때문에 그 이하 버전에서는 불가능하다.  
앱이 바로 크래시 발생하고 종료된다.

때문에 그 이하 버젼을 지원 하려면 쓰레드 운영에 대한 처리를 학습해야 한다.

