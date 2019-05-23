# 권한
> 권한 등록의 목적은 사용자에게 앱이 어떤 기능을 사용하는지 알려준다. 

* 권한 고지를 하고 사용 허가를 받아야 하는 목록

http://developer.android.com/guide/topics/permissions/requesting.html

권한 등록은 manifests 에서 등록 하면 된다. 더블클릭시 나오는 AndroidManifest.xml 에 등록 하면 된다.

위치는 <application></application> 바깥 <manifest></manifest> 안쪽 이다.
```Xml
<manifest>
    <application>
        ...
    </application>
    
    <uses-permission android:name="android.permission.INTERNET"/>
</manifest>
```

인터넷 권한은 네트워크를 사용할 때 등록 해준다.

```Xml
    <uses-permission android:name="android.permission.INTERNET"/>
```
개인정보와 관련된 권한이 아니기 때문에 **앱 인포 \> 퍼미션**에 보이지는 않는다.

```Xml
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
```
위치정보 권한이다.

개인정보와 관련 되어 있기 때문에 **퍼미션 \> 로케이션**이 등록된다.

비슷한 정보의 권한은 하나로 컨트롤 한다.


```Xml
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
    <uses-permission android:name="android.permission.READ_CONTACTS"/>
    <uses-permission android:name="android.permission.WRITE_CONTACTS"/>
    <uses-permission android:name="android.permission.SEND_SMS"/>
    <uses-permission android:name="android.permission.RECEIVE_SMS"/>
```

등록한 권한들 중에서 사용자에게 권한 요청을 할 목록을 메인 엑티비티.kt에 만들어 주어야 한다.


```Kotlin
class MainActivity : AppCompatActivity() {
    
    var permission_list = arrayOf(      // 사용할 권한 목록을 배열로 작성해 둔다.
        android.Manifest.permission.ACCESS_FINE_LOCATION,      //Manifest는 android 것이다. java.util 아님
        android.Manifest.permission.ACCESS_COARSE_LOCATION,
        android.Manifest.permission.READ_CONTACTS,
        android.Manifest.permission.WRITE_CONTACTS,
        android.Manifest.permission.SEND_SMS,
        android.Manifest.permission.RECEIVE_SMS
    )
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        checkPermission()   // 우리가 만들 함수
    }
    
    fun checkPermission() { // 이 작업은 안드로이드 마시멜로우 이상부터 해야한다.
        if(Build.VERSION.SDK_INT < Build.VERSION_CODES.M) {  // 이하라면 그냥 종료
            return;
        }
        for(permission : String in permission_list) {
            var chk = checkCallingOrSelfPermission(permission)
            
            if(chk == PackageManager.PERMISSION_DENIED) {   // 권한 목록 중 꺼져있는것이 있으면
                requestPermissions(permission_list, 0)       // 권한 요청
                break
            }
        }
    }
}
```

```Kotlin
class MainActivity : AppCompatActivity() {

    var permission_list = arrayOf(
        android.Manifest.permission.ACCESS_FINE_LOCATION,      //Manifest는 android 것이다. java.util 아님
        android.Manifest.permission.ACCESS_COARSE_LOCATION,
        android.Manifest.permission.READ_CONTACTS,
        android.Manifest.permission.WRITE_CONTACTS,
        android.Manifest.permission.SEND_SMS,
        android.Manifest.permission.RECEIVE_SMS
    )

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        checkPermission()   // 우리가 만들 함수
    }

    fun checkPermission() { // 이 작업은 안드로이드 마시멜로우 이상부터 해야한다.
        if(Build.VERSION.SDK_INT < Build.VERSION_CODES.M) {  // 이하라면 그냥 종료
            return;
        }
        for(permission : String in permission_list) {
            var chk = checkCallingOrSelfPermission(permission)

            if(chk == PackageManager.PERMISSION_DENIED) {   // 권한 목록 중 꺼져있는것이 있으면
                requestPermissions(permission_list,0)       // 권한 요청
                break
            }
        }
    }
    override fun onRequestPermissionsResult(requestCode: Int, permissions: Array<out String>, grantResults: IntArray) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults)


        var idx = 0

        textView.text = ""

        for (idx in grantResults.indices) {     // .indices 를 빼주게 되면 idx에 grantResults의 배열값이 하나씩 들어가게 되는데, 넣으면 인덱스 번호가 들어가게 된다.
            if (grantResults[idx] == PackageManager.PERMISSION_GRANTED) {
                textView.append("${permission_list[idx]} : 허용함\n")
            } else {
                textView.append("${permission_list[idx]} : 허용하지 않음\n")
            }
        }
    }
}
```

~~.append 와 ${ } 알아보기~~
