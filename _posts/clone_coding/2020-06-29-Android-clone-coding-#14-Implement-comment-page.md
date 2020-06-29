---
title: "하울 안드로이드 인스타그램 클론 코딩 #14 덧글 페이지 만들기"
excerpt: "게시물의 덧글을 파이어베이스에 저장하고 리사이클러 뷰로 표현"
classes: wide
categories: 
  - clone_coding
tag:
  - android
  - clone_coding
  - howlstagram
read_time: false
last_modified_at: 2020-06-29T19:05:00+09:00
toc: true
---

## activity_comment.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".navigation.CommentActivity">

    <androidx.appcompat.widget.Toolbar
        android:id="@+id/my_toolbar"
        android:layout_width="match_parent"
        android:layout_height="35dp">

        <ImageView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:src="@drawable/logo_title" />
    </androidx.appcompat.widget.Toolbar>

    <LinearLayout
        android:id="@+id/toolbar_division"
        android:layout_width="match_parent"
        android:layout_below="@+id/my_toolbar"
        android:layout_height="1dp"
        android:background="@color/colorDivision"
        android:orientation="horizontal" />

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/comment_recyclerview"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_below="@+id/toolbar_division"
        android:layout_marginTop="8dp"
        android:layout_marginBottom="30dp" />

    <Button
        android:id="@+id/comment_btn_send"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@+id/comment_recyclerview"
        android:layout_alignParentRight="true"
        android:layout_margin="10dp"
        android:text="@string/send" />

    <EditText
        android:id="@+id/comment_edit_message"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_below="@+id/comment_recyclerview"
        android:layout_margin="10dp"
        android:layout_toLeftOf="@+id/comment_btn_send" />

</RelativeLayout>
```

게시물의 댓글 페이지를 나타내는 레이아웃.

상단에 로고와 분할 선이 있고 그 아래에 사용자들의 댓글이 리사이클러 뷰로 표현된다.



## item_comment.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="50dp"
    android:orientation="horizontal">

    <ImageView
        android:id="@+id/commentviewitem_imageview_profile"
        android:layout_width="35dp"
        android:layout_height="35dp"
        android:layout_margin="7.5dp" />

    <TextView
        android:id="@+id/commentviewitem_textview_profile"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_margin="7.5dp"
        android:text="UserID"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/commentviewitem_textview_comment"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_margin="7.5dp"
        android:text="Message" />


</LinearLayout>
```

댓글을 표시하는 레이아웃.

사용자의 프로필 사진, 아이디, 댓글이 표시된다.



## DetailViewFragment

**onBindViewHolder**

```kotlin
override fun onBindViewHolder(p0: RecyclerView.ViewHolder, p1: Int) {
    // ,,,
    
    viewholder.detailviewitem_comment_imageview.setOnClickListener { v ->
        var intent = Intent(v.context, CommentActivity::class.java)
        intent.putExtra("contentUid", contentUidList[p1])
        startActivity(intent)
    }
}
```

댓글 이미지를 선택하면 댓글 페이지로 이동할 수 있도록 리사이클러뷰에서 이미지가 바인드될 때 이미지 뷰의 클릭 리스너를 지정.

인텐트로 작성자의 uid를 넘겨준다.



## CommentActivity

```kotlin
class CommentActivity : AppCompatActivity() {
    var contentUid: String? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_comment)
        contentUid = intent.getStringExtra("contentUid")

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

            comment_edit_message.setText("")

        }
    }

    inner class CommentRecyclerviewAdapter : RecyclerView.Adapter<RecyclerView.ViewHolder>() {
        var comments: ArrayList<ContentDTO.Comment> = arrayListOf()

        init {
            FirebaseFirestore.getInstance()
                .collection("images")
                .document(contentUid!!)
                .collection("comments")
                .orderBy("timestamp")
                .addSnapshotListener { querySnapshot, firebaseFirestoreException ->
                    comments.clear()
                    if (querySnapshot == null)
                        return@addSnapshotListener

                    for (snapshot in querySnapshot.documents!!) {
                        comments.add(snapshot.toObject(ContentDTO.Comment::class.java)!!)
                        notifyDataSetChanged()
                    }
                }
        }

        override fun onCreateViewHolder(p0: ViewGroup, p1: Int): RecyclerView.ViewHolder {
            var view = LayoutInflater.from(p0.context).inflate(R.layout.item_comment, p0, false)
            return CustomViewHolder(view)
        }

        private inner class CustomViewHolder(view: View) : RecyclerView.ViewHolder(view)

        override fun getItemCount(): Int {
            return comments.size
        }

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
                        Glide.with(p0.itemView.context).load(url).apply(RequestOptions().circleCrop()).into(view.commentviewitem_imageview_profile)
                    }
                }

        }

    }
}
```

**onCreate**

- 리사이클러 뷰의 어댑터와 레이아웃 매니저 지정.
- 댓글을 작성하는 **send** 버튼의 클릭 리스너 지정.

**CommentRecyclerviewAdapter**

- **init**
  - 리사이클러 뷰가 생성될 때 현재 게시물의 댓글들을 시간순으로 정렬하여 리스트에 추가.
- **onBindViewHolder**
  - 데이터가 바인드될 때 아이디와 댓글 내용을 리스트에서 가져오고 파이어베이스에서 프로필 이미지의 url 을 받아 Glide 로 이미지 뷰에 바인딩.



