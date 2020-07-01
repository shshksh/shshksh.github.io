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

```kotlin
fun requestFollow() {
    var tsDocFollowing = firestore?.collection("users")?.document(currentUserId!!)
    firestore?.runTransaction { transaction ->
        var followDTO = transaction.get(tsDocFollowing!!).toObject(FollowDTO::class.java)
        if (followDTO == null) {
            followDTO = FollowDTO()
            followDTO!!.followingCount = 1
            followDTO!!.followings[uid!!] = true

            transaction.set(tsDocFollowing, followDTO)
            return@runTransaction
        }

        if (followDTO.followings.containsKey(uid)) {
            followDTO?.followingCount = followDTO?.followingCount - 1
            followDTO?.followings?.remove(uid)
        } else {
            followDTO?.followingCount = followDTO?.followingCount + 1
            followDTO?.followings[uid!!] = true
        }
        transaction.set(tsDocFollowing, followDTO)
        return@runTransaction
    }

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



## DetailViewFragment



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
            favoriteAlarm(contentDTOs[position].uid!!)
        }
        transaction.set(tsDoc, contentDTO)
    }
}
```



```kotlin
override fun onBindViewHolder(p0: RecyclerView.ViewHolder, p1: Int) {
    var viewholder = (p0 as CustomViewHolder).itemView

    viewholder.detailviewitem_profile_textview.text = contentDTOs!![p1].userId

    Glide.with(p0.itemView.context).load(contentDTOs!![p1].imageUrl)
        .into(viewholder.detailviewitem_imageview_content)

    viewholder.detailviewitem_explain_textview.text = contentDTOs!![p1].explain

    viewholder.detailviewitem_favoritecounter_textview.text = "Likes " + contentDTOs!![p1].favoriteCount

    viewholder.detailviewitem_favorite_imageview.setOnClickListener {
        favoriteEvent(p1)
    }

    if (contentDTOs!![p1].favorites.containsKey(uid)) {
        viewholder.detailviewitem_favorite_imageview.setImageResource(R.drawable.ic_favorite)
    } else {
        viewholder.detailviewitem_favorite_imageview.setImageResource(R.drawable.ic_favorite_border)
    }

    viewholder.detailviewitem_profile_image.setOnClickListener {
        var fragment = UserFragment()
        var bundle = Bundle()
        bundle.putString("destinationUid", contentDTOs[p1].uid)
        bundle.putString("userId", contentDTOs[p1].userId)
        fragment.arguments = bundle
        activity?.supportFragmentManager?.beginTransaction()?.replace(R.id.main_content, fragment)?.commit()
    }
    viewholder.detailviewitem_comment_imageview.setOnClickListener { v ->
        var intent = Intent(v.context, CommentActivity::class.java)
        intent.putExtra("contentUid", contentUidList[p1])
        intent.putExtra("destinationUid", contentDTOs[p1].uid)
        startActivity(intent)
    }
}
```



## CommentActivity

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_comment)
    contentUid = intent.getStringExtra("contentUid")
    destinationUid = intent.getStringExtra("destinationUid")

    comment_recyclerview.adapter = CommentRecyclerviewAdapter()
    comment_recyclerview.layoutManager = LinearLayoutManager(this)

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



```kotlin
override fun onBindViewHolder(p0: RecyclerView.ViewHolder, p1: Int) {
    var view = p0.itemView
    view.commentviewitem_textview_comment.text = comments[p1].comment
    view.commentviewitem_textview_profile.text = comments[p1].userId

    FirebaseFirestore.getInstance()
        .collection("profileImages")
        .document(comments[p1].uid!!)
        .get()
        .addOnCompleteListener { task ->
            if (task.isSuccessful) {
                var url = task.result!!["image"]
         		 Glide.with(p0.itemView.context).load(url).apply(RequestOptions().circleCrop())
                    .into(view.commentviewitem_imageview_profile)
            }
        }

}
```

