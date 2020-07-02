---
title: "하울 안드로이드 인스타그램 클론 코딩 #15 알람 이벤트 만들기"
excerpt: ""
classes: wide
categories: 
  - clone_coding
tag:
  - android
  - clone_coding
  - howlstagram
read_time: false
last_modified_at: 2020-07-01T18:05:00+09:00
toc: true
---



## AlarmDTO

```kotlin
data class AlarmDTO(
    var destinationUid: String? = null,
    var userId: String? = null,
    var uid: String? = null,
    var kind: Int? = null,
    var message: String? = null,
    var timestamp: Long? = null
)
```

알람 데이터를 관리하는 모델



## UserFragment

**requestFollow**

```kotlin
fun requestFollow() {
    // ...
    var tsDocFollower = firestore?.collection("users")?.document(uid!!)
    firestore?.runTransaction { transaction ->
        var followDTO = transaction.get(tsDocFollower!!).toObject((FollowDTO::class.java))
        if (followDTO == null) {
            followDTO = FollowDTO()
            followDTO!!.followerCount = 1
            followDTO!!.followers[currentUserId!!] = true
            followerAlarm(uid!!)

            transaction.set(tsDocFollower, followDTO!!)
            return@runTransaction
        }

        if (followDTO!!.followers.containsKey(currentUserId)) {
            followDTO!!.followerCount = followDTO!!.followerCount - 1
            followDTO!!.followers.remove(currentUserId!!)
        } else {
            followDTO!!.followerCount = followDTO!!.followerCount + 1
            followDTO!!.followers[currentUserId!!] = true
            followerAlarm(uid!!)
        }
        transaction.set(tsDocFollower, followDTO!!)
        return@runTransaction
    }
}
```

팔로우를 누를 때 마다 follwer 알람 호출



**followerAlarm**

```kotlin
fun followerAlarm(destinationUid: String) {
    var alarmDTO = AlarmDTO()
    alarmDTO.destinationUid = destinationUid
    alarmDTO.userId = auth?.currentUser?.email
    alarmDTO.uid = auth?.currentUser?.uid
    alarmDTO.kind = 2
    alarmDTO.timestamp = System.currentTimeMillis()
    FirebaseFirestore.getInstance().collection("alarms").document().set(alarmDTO)
}
```

팔로우 데이터를 저장하는 모델



## DetailViewFragment

**onBindViewHolder**

```kotlin
override fun onBindViewHolder(p0: RecyclerView.ViewHolder, p1: Int) {
    viewholder.detailviewitem_comment_imageview.setOnClickListener { v ->
        var intent = Intent(v.context, CommentActivity::class.java)
        intent.putExtra("contentUid", contentUidList[p1])
        intent.putExtra("destinationUid", contentDTOs[p1].uid)
        startActivity(intent)
    }
}
```

댓글 이미지 클릭 시 CommentActivity에 destinationUid 를 인텐트로 넘겨주는 코드 추가.



**favoriteEvent**

```kotlin
fun favoriteEvent(position: Int) {
    var tsDoc = firestore?.collection("images")?.document(contentUidList[position])
    firestore?.runTransaction { transaction ->

        var contentDTO = transaction.get(tsDoc!!).toObject(ContentDTO::class.java)

        if (contentDTO!!.favorites.containsKey(uid)) {
            contentDTO?.favoriteCount = contentDTO?.favoriteCount?.minus(1)
            contentDTO?.favorites.remove(uid)
        } else {
            contentDTO?.favoriteCount = contentDTO?.favoriteCount?.plus(1)
            contentDTO?.favorites[uid!!] = true
            favoriteAlarm(contentDTOs[position].uid!!) // 추가된 라인
        }
        transaction.set(tsDoc, contentDTO)
    }
}
```

좋아요 버튼 클릭 시 알람을 울리는 라인 추가



**favoriteAlarm**

```kotlin
fun favoriteAlarm(destinationUid: String) {
    var alarmDTO = AlarmDTO()
    alarmDTO.destinationUid = destinationUid
    alarmDTO.userId = FirebaseAuth.getInstance().currentUser?.email
    alarmDTO.uid = FirebaseAuth.getInstance().currentUser?.uid
    alarmDTO.kind = 0
    alarmDTO.timestamp = System.currentTimeMillis()
    FirebaseFirestore.getInstance().collection("alarms").document().set(alarmDTO)
}
```

알람 데이터 모델을 데이터 베이스에 저장.



## CommentActivity

**onCreate**

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    // ...
    destinationUid = intent.getStringExtra("destinationUid")

    // ...

    comment_btn_send?.setOnClickListener {
        var comment = ContentDTO.Comment()
        comment.userId = FirebaseAuth.getInstance().currentUser?.email
        comment.uid = FirebaseAuth.getInstance().currentUser?.uid
        comment.comment = comment_edit_message.text.toString()
        comment.timestamp = System.currentTimeMillis()
        FirebaseFirestore.getInstance().collection("images").document(contentUid!!).collection("comments")
        .document().set(comment)
        commentAlarm(destinationUid!!, comment_edit_message.text.toString())
        comment_edit_message.setText("")
    }
}
```

인텐트로부터 destinationUid 를 받아 초기화.

댓글 버튼의 클릭 리스너에 알람을 울리는 코드를 추가.

**commentAlarm**

```kotlin
fun commentAlarm(destinationUid: String, message: String) {
    var alarmDTO = AlarmDTO()
    alarmDTO.destinationUid = destinationUid
    alarmDTO.userId = FirebaseAuth.getInstance().currentUser?.email
    alarmDTO.uid = FirebaseAuth.getInstance().currentUser?.uid
    alarmDTO.timestamp = System.currentTimeMillis()
    alarmDTO.message = message
    FirebaseFirestore.getInstance().collection("alarms").document().set(alarmDTO)
}
```

알람의 데이터 모델을 만들어 데이터베이스에 저장.



**onBindViewHolder**

```kotlin
override fun onBindViewHolder(p0: RecyclerView.ViewHolder, p1: Int) {
    // ...
         		 Glide.with(p0.itemView.context).load(url).apply(RequestOptions().circleCrop())
                    .into(view.commentviewitem_imageview_profile)
            }
        }

}
```

댓글 작성자의 프로필 사진을 가져와서 바인딩.