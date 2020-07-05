---
title: "하울 안드로이드 인스타그램 클론 코딩 #18 푸시 이벤트 만들기"
excerpt: ""
classes: wide
categories: 
  - clone_coding
tag:
  - android
  - clone_coding
  - howlstagram
read_time: false
last_modified_at: 2020-07-05T19:0:00+09:00
toc: true
---



## PushDTO

```kotlin
data class PushDTO(
    var to: String? = null,
    var notification: Notification = Notification()
) {
    data class Notification(
        var body: String? = null,
        var title: String? = null
    )
}
```

푸시 메시지 정보를 담는 데이터 모델

## FcmPush

```kotlin
class FcmPush {
    var JSON = MediaType.parse("application/json; charset=utf-8")
    var url = "https://fcm.googleapis.com/fcm/send"
    var serverKey = "AIzaSyASyuccOcl_pMz1OJsAMfDbuFa5ihUhpCU"
    var gson: Gson? = null
    var okHttpClient: OkHttpClient? = null

    companion object {
        var instance = FcmPush()
    }

    init {
        gson = Gson()
        okHttpClient = OkHttpClient()
    }

    fun sendMessage(destinationUid: String, title: String, message: String) {
        FirebaseFirestore.getInstance().collection("pushtokens").document(destinationUid).get()
            .addOnCompleteListener { task ->
                if (task.isSuccessful) {
                    var token = task?.result?.get("pushToken").toString()

                    var pushDTO = PushDTO()
                    pushDTO.to = token
                    pushDTO.notification.title = title
                    pushDTO.notification.body = message

                    var body = RequestBody.create(JSON, gson?.toJson(pushDTO))
                    var request = Request.Builder().addHeader("Content-Type", "application/json")
                        .addHeader("Authorization", "key=" + serverKey)
                        .url(url)
                        .post(body)
                        .build()

                    okHttpClient?.newCall(request)?.enqueue(object : Callback {
                        override fun onFailure(call: Call?, e: IOException?) {

                        }

                        override fun onResponse(call: Call?, response: Response?) {
                            println(response?.body()?.string())
                        }
                    })
                }
            }
    }
}
```

푸시 메시지를 보내주는 기능을 가진 클래스

## UserFragment

```kotlin
fun followerAlarm(destinationUid: String) {
    // ...

    var message = auth?.currentUser?.email + getString(R.string.alarm_follow)
    FcmPush.instance.sendMessage(destinationUid, "Howlstagram", message)
}
```

## DetailViewFragment

```kotlin
fun favoriteAlarm(destinationUid: String) {
    // ...

    var message = FirebaseAuth.getInstance()?.currentUser?.email + getString(R.string.alarm_favorite)
    FcmPush.instance.sendMessage(destinationUid, "Howlstagram", message)
}
```

## CommentActivity

```kotlin
fun commentAlarm(destinationUid: String, message: String) {
    // ...

    var msg = FirebaseAuth.getInstance().currentUser?.email + " " + getString(R.string.alarm_comment) + " of " + message
    FcmPush.instance.sendMessage(destinationUid, "Howlstagram", msg)
}
```

팔로우, 좋아요, 댓글 알람 호출 시 추가로 푸시 메시지를 송신하는 라인 추가