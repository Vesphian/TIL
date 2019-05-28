# Async Task
* 비동기 처리를 위해 제공되는 클래스이다.  
* 개발자가 발생 시키는 쓰레드와 핸들러의 조합으로 쓰레드 운영 중 화면 처리가 가능했던 구조를 클래스로 제공하는 것이다.  
* 일반적으로 4가지 메소드를 오버라이드 해주어서 사용이 가능하다.

## 메소드
* onProgressUpdate : doInBackground 메소드에서 publishProgress 메소드를 호출하면 Main Thread가 처리하는 메소드. **doInBackground 메소드 내에서 화면 처리가 필요할 때** 사용한다.  
* onPostExecute : doInBackground 메소드 수행 완료 후 호출. Main Thread가 처리한다.  
* onPreExecute : doInBackground 메소드가 호출되기 전에 호출되는 메소드. 역시 메인 스레드가 처리한다. 최초에 딱 한번.  
* doInBackground : 일반 쓰레드에서 처리한다. 화면에 관련된 작업은 하면 안되고, 5초 이상 소요되는 작업 가능.

```Kotlin
package com.vesphian.asynctask

import android.os.AsyncTask
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.os.SystemClock
import android.util.Log
import kotlinx.android.synthetic.main.activity_main.*

class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        button.setOnClickListener { view ->
            var time = System.currentTimeMillis()
            textView.text = "버튼 클릭 : ${time}"

        }
        var sync = AsyncTaskClass()
        sync.execute(10, 20)
    }


    inner class AsyncTaskClass : AsyncTask<Int, Long, String>() {
        override fun onPreExecute() {
            super.onPreExecute()
            textView2.text = "AsyncTask 가동"
        }

        override fun doInBackground(vararg params: Int?): String {
            var a1 = params[0]!!
            var a2 = params[1]!!

            for (idx in 0..9){
                SystemClock.sleep(100)

                a1++
                a2++

                Log.d("test1", "${idx} : ${a1}, ${a2}")
            }
            
            return "문자열"
        }
    }
}
```

코드에 sync.execute()에는 vararg params: Int! 라는 파라메터가 들어가게 되는데 vararg 타입은 가변형 매개변수라고 해서 값을 넘기는 갯수에 따라 해당 갯수 크기의 배열이 만들어져 넘어간다.  
또한 이 vararg의 값은 doInBackground의 매개변수로 들어오게 된다.

null을 허용하는 변수의 값을 null을 허용하지 **않는** 변수에 넣을때는 느낌표 두개(!!)를 허용하는 변수의 뒤에 붙여주어야 한다.  

```Kotlin
        override fun doInBackground(vararg params: Int?): String {
            var a1 = params[0]!!
            var a2 = params[1]!!

            for (idx in 0..9){
                SystemClock.sleep(100)

                a1++
                a2++

                Log.d("test1", "${idx} : ${a1}, ${a2}")

                var time = System.currentTimeMillis()
                publishProgress(time)
            }

            return "문자열"
        }

        override fun onProgressUpdate(vararg values: Long?) {
            super.onProgressUpdate(*values)

            textView2.text = "Async : ${values[0]}"
        }
```

이렇게 화면에 관련해서 처리를 하려면 publishProgress()를 호출하여 onProgressUpdate를 작동하게 한다.

마지막으로 남은 onPostExecute()는 String을 받는데 doInBackground()의 반환형이 바로 String이다.  
따라서 doInBackground의 결과값을 받으면 이렇게 된다.  

```Kotlin
        override fun doInBackground(vararg params: Int?): String {
            var a1 = params[0]!!
            var a2 = params[1]!!

            for (idx in 0..9){
                SystemClock.sleep(100)

                a1++
                a2++

                Log.d("test1", "${idx} : ${a1}, ${a2}")

                var time = System.currentTimeMillis()
                publishProgress(time)
            }

            return "수행이 완료되었습니다."
        }

        override fun onProgressUpdate(vararg values: Long?) {
            super.onProgressUpdate(*values)

            textView2.text = "Async : ${values[0]}"
        }

        override fun onPostExecute(result: String?) {
            super.onPostExecute(result)
            textView2.text = result
        }
```
