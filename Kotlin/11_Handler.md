# 핸들러

* 메인 쓰레드에서 처리하는 코드 (액티비티 내의 코드) 중에 일정 작업을 계속 반복처리해야 할 경우가 있다.  
* 이때 무한 루프를 쓰면 화면처리가 불가능하다.  
* 핸들러를 통하면 원하는 코드를 반복해서 작업하는 것이 가능하다.

* 핸들러는 개발자가 안드로이드 OS에게 작업수행을 요청하는 역할을 한다.  
* 개발자가 작업을 요청하면 안드로이드 OS는 작업을 하지 않을때 개발자가 요청한 작업을 처리하게 된다. 
    (어차피 네트워크 관련 코드는 개발자가 운영하는 일반 쓰레드로 빼놓아야 하므로 메인쓰레드는 여유가 있다.)
* 메인 쓰레드에서 처리를 해야 하므로 5초 이상 걸리는 작업은 불가.

```Kotlin
package com.vesphian.handler

import android.os.Bundle
import android.os.Handler
import android.os.SystemClock
import android.support.v7.app.AppCompatActivity
import kotlinx.android.synthetic.main.activity_main.*

class MainActivity : AppCompatActivity() {

    var handler : Handler? = null   // 핸들러 객체를 담을 변수

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        currentTimebutton.setOnClickListener { view ->
            var time = System.currentTimeMillis()
            textView.text = "버튼 클릭 : ${time}"
        }

//        while (true) {
//            SystemClock.sleep(100)
//            var time = System.currentTimeMillis()
//            textView2.text = "while : ${time}"
//        }

        handler = Handler()     // 객체 생성

        var thread = ThreadClass()
        handler?.post(thread)   // post 메소드는 안드로이드OS가 한가할때 thread 객체를 실행한다.
//        handler?.postDelayed(thread, 100) // 100 미리 세컨드 후에 핸들러 동작

    }

    inner class ThreadClass : Thread(){
        override fun run() {
            var time = System.currentTimeMillis()
            textView2.text = "handler : ${time}"

            handler?.post(this)
//            handler?.postDelayed(this)
        }
    }
}
```
여기서 inner class를 무한루프로 하면 주석 처리한 부분처럼 메인쓰레드를 무한으로 잡고 있게 되므로 안된다.  
5초 미만의 빨리 끝나는 작업을 하고 싶을때는 핸들러
뭔가 계속해서 길게 처리하려면 쓰레드를 쓰면 된다.
하지만 코드 중간에 화면 갱신이 필요하다면 핸들러를 사용해야한다.

# 핸들러를 통한 화면처리
* 안드로이드에서는 네트워크에 관련된 처리나 5초이상 걸리는 작업이 메인쓰레드에서 처리되면 ANR로 크러시가 발생하기 때문에 개발자가 발생시킨 쓰레드에서 처리해야한다.  
* 개발자가 발생하는 쓰레드에서 화면처리를 하고자 할 때 핸들러를 이용한다.  
* 안드로이드 오레오(8.0) 이상에서는 개발자가 발생한 쓰레드에서 화면처리를 할 수 있다.  
* 쓰레드에서 코드 처리 중 화면에 관련된 작업이 필요하다면 핸들러를 상속받은 클래스를 만들어 필요시 핸들러를 요청하면 된다.  
* 핸들러를 상속 받은 클래스에 만든 메서드는 메인 쓰레드에서 처리한다. (5초 이상 걸리면 안된다.)

```Kotlin
package com.vesphian.threadhandler

import android.support.v7.app.AppCompatActivity
import android.os.Bundle
import android.os.Handler
import android.os.Message
import android.os.SystemClock
import android.provider.Settings
import android.util.Log
import kotlinx.android.synthetic.main.activity_main.*

class MainActivity : AppCompatActivity() {

    var isRunning:Boolean = false
    var handler:DisplayHandler? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        currentTimeButton.setOnClickListener { view ->
            var time = System.currentTimeMillis()
            textView.text = "버튼클릭 : ${time}"
        }

        handler = DisplayHandler()

        isRunning = true
        var thread = ThreadClass()
        thread.start()
    }

    override fun onDestroy() {
        super.onDestroy()

        isRunning = false
    }

    inner class ThreadClass : Thread() {
        override fun run() {
            while (isRunning){
                SystemClock.sleep(100)
                var time = System.currentTimeMillis()
                Log.d("test1", "쓰레드 : ${time}")
//                textView2.text = "쓰레드 : ${time}"    // 이렇게 하면 오레오 이하에서는 실행하자 마자 오류 발생한다.
//                handler?.sendEmptyMessage(0)
                var msg = Message()
                msg.what = 0
                msg.obj = time
                handler?.sendMessage(msg)
            }
        }
    }

    inner class DisplayHandler : Handler(){
        override fun handleMessage(msg: Message?) {
            super.handleMessage(msg)

//            var time = System.currentTimeMillis()
//            textView2.text = "Handler : ${time}"
            textView2.text = "Handler : ${msg?.obj}"
        }
    }
}
```
위 처럼 **메세지 객체**를 만들어서 **메인 쓰레드**에서 **만든 어떤 값**을 **핸들러로 전달**해서 **화면 작업**을 할 때 사용 할 수 있게 된다.  
여기서 msg.obj는 객체를 집어넣는 곳인데 오토박싱으로 long 타입의 값을 자동으로 객체로 만들어서 들어간다.  
객체로 보내는 것이기 때문에 클래스를 설계해서 변수 10개 설정 해서 클래스를 넘기거나 배열을 넘기거나 하면 한번에 여러개 값을 넘길수 있다.  

또 msg.what은 경우에 따라서 보내는 형식을 나눌 때 사용한다.
예를 들면
```Kotlin
    inner class DisplayHandler : Handler(){
        override fun handleMessage(msg: Message?) {
            super.handleMessage(msg)

            if(msg?.what == 0){
//            var time = System.currentTimeMillis()
                textView2.text = "Handler : ${msg?.obj}"
            }
        }
    }
```
이렇게 조건을 주어서 처리 할 수 있다.  
메세지 객체는 정수 2개 객체 하나를 세팅 할 수 있다.

```Kotlin
    inner class DisplayHandler : Handler() {
        override fun handleMessage(msg: Message?) {
            super.handleMessage(msg)

            if (msg?.what == 0) {
//            var time = System.currentTimeMillis()
                textView2.text = "Handler : ${msg?.obj}"
            } else if (msg?.what == 1) {
                textView2.text = "${msg?.arg1}, ${msg?.arg2}, ${msg?.obj}"
            }
        }
    }
```
이렇게 여러값을 넘길 수 있다.

* 개발자가 만든 쓰레드에서 화면 처리를 하고 싶으면 handler를 사용 하면 된다.
