---
title: "하울 안드로이드 인스타그램 클론 코딩 #17 푸시 라이브러리 설치하기"
excerpt: ""
classes: wide
categories: 
  - clone_coding
tag:
  - android
  - clone_coding
  - howlstagram
read_time: false
last_modified_at: 2020-07-05T16:45:00+09:00
toc: true
---

## manifests

```xml
<meta-data
    android:name="com.google.firebase.messaging.default_notification_icon"
    android:resource="@drawable/push_icon" />
<meta-data
    android:name="com.google.firebase.messaging.default_notification_color"
    android:resource="@color/colorAccent" />
```

파이어베이스 푸시 알람을 위해 메타 데이터 추가

## MainActivity

**onCreate**

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)
    bottom_navigation.setOnNavigationItemSelectedListener(this)
    ActivityCompat.requestPermissions(this, arrayOf(Manifest.permission.READ_EXTERNAL_STORAGE), 1)

    bottom_navigation.selectedItemId = R.id.action_home
    registerPushToken()
}
```

메인 액티비티가 시작될 때 registerPushToken 호출

**registerPushToken**

```kotlin
fun registerPushToken() {
    FirebaseInstanceId.getInstance().instanceId.addOnCompleteListener { task ->
        val token = task.result?.token
        val uid = FirebaseAuth.getInstance().currentUser?.uid
        val map = mutableMapOf<String, Any>()
        map["pushToken"] = token!!

        FirebaseFirestore.getInstance().collection("pushtokens").document(uid!!).set(map)
    }
}
```

토큰을 생성해 데이터베이스에 저장

