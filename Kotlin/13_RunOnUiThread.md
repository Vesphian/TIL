# RunOnUiThread
* 해당 메소드는 개발자가 발생시킨 일반 쓰레드에서 코드 일부를 메인 쓰레드가 처리하도록 하는 메서드이다.  
* 자바에서는 메서드로 제공되나 코틀린에서는 람다식으로 제공되고 있으므로 작성하는것이 간편하다.

```Kotlin
package com.vesphian.runonuithread

import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.os.SystemClock
import android.util.Log
import kotlinx.android.synthetic.main.activity_main.*

class MainActivity : AppCompatActivity() {


    var isRunning: Boolean = false
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        button.setOnClickListener {
            var time = System.currentTimeMillis()

            textView.text = "버튼 클릭 : ${time}"
        }
        isRunning = true
        var thread = ThreadClass()
        thread.start()

    }

    inner class ThreadClass : Thread() {
        override fun run() {
            super.run()

            while (isRunning) {
                SystemClock.sleep(100)
                var time = System.currentTimeMillis()
                Log.d("test1", "쓰레드 : ${time}")

                runOnUiThread {
                    textView2.text = "쓰레드 : ${time}"
                }
            }
        }
    }

    override fun onDestroy() {
        super.onDestroy()
        isRunning = false
    }
}
```

위의 코드 중에서 
```Kotlin
    inner class ThreadClass : Thread() {
        override fun run() {
            super.run()

            while (isRunning) {
                SystemClock.sleep(100)
                var time = System.currentTimeMillis()
                Log.d("test1", "쓰레드 : ${time}")

                runOnUiThread {
                    textView2.text = "쓰레드 : ${time}"
                }
            }
        }
    }

```
부분에 runOnUiThread 부분을 만나면 메인 쓰레드로 작업을 넘겨서 처리하고 다시 사용자 쓰레드로 돌아오는 작업을 통해서 안드로이드 7.1 이하에서 발생하는 오류를 처리해 줄 수 있다.  
이전에 사용했던 핸들러나 AsyncTask로 처리하는 것 보다 간편하게 처리 가능하다.
