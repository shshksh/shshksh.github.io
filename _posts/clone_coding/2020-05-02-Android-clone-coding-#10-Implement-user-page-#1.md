---
title: "하울 안드로이드 인스타그램 클론 코딩 #10 유저 페이지 구현 #1"
excerpt: "유저 메뉴 클릭 시 데이터를 recycler view에 담아 표시"
classes: wide
categories: 
  - clone_coding
tag:
  - android
  - clone_coding
  - howlstagram
read_time: false
last_modified_at: 2020-05-02T22:17:15+09:00
toc: true
---

> 출처: [인프런-하울의 안드로이드 인스타그램 클론 만들기]([https://www.inflearn.com/course/%EC%9D%B8%EC%8A%A4%ED%83%80%EA%B7%B8%EB%9E%A8%EB%A7%8C%EB%93%A4%EA%B8%B0-%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C/dashboard](https://www.inflearn.com/course/인스타그램만들기-안드로이드/dashboard))


# fragment_user.xml

**구조**

```
├──LiniarLayout
|  ├──RelatibeLayout
|  |  ├──ImageView
|  |  ├──Button
|  |  ├──LinearLayout
|  |  |  ├──LinearLayout
|  |  |  |  ├──TextView
|  |  |  |  └──TextView
|  |  |  ├──LinearLayout
|  |  |  |  ├──TextView
|  |  |  |  └──TextView
|  |  |  ├──LinearLayout
|  |  |  |  ├──TextView
|  |  |  |  └──TextView
|  └──RecyclerView
```



**루트 레이아웃**

```xml
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    
    <!-- ... -->
    
</LinearLayout>
```



**상단 뷰 그룹**

```xml
<RelativeLayout
    android:layout_width="match_parent"
    android:layout_height="100dp">
    
    <!-- ... -->    
    
</RelativeLayout>
```



**프로필 이미지**

```xml
<ImageView
    android:id="@+id/account_iv_profile"
    android:layout_width="100dp"
    android:layout_height="100dp"
    android:layout_alignParentLeft="true"
    android:src="@drawable/ic_account" />
```



**팔로우 버튼**

```xml
<Button
    android:id="@+id/account_btn_follow_signout"
    android:layout_width="match_parent"
    android:layout_height="40dp"
    android:layout_alignParentBottom="true"
    android:layout_toRightOf="@+id/account_iv_profile"
    android:text="@string/follow" />
```



**사용자 정보 뷰 그룹**

```xml
<LinearLayout
    android:gravity="center"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_above="@+id/account_btn_follow_signout"
    android:layout_toRightOf="@+id/account_iv_profile">
<!-- ... -->
</LinearLayout>
```



**사용자 정보 X 3**

```xml
<LinearLayout
    android:layout_width="0dp"
    android:layout_height="wrap_content"
    android:layout_weight="1"
    android:gravity="center"
    android:orientation="vertical">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/post"/>
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/account_tv_post_count"
        android:text="@string/count_0"/>
</LinearLayout>
```

각각 post, follow, following



**업로드한 이미지를 보여주는 RecyclerView**

```xml
<androidx.recyclerview.widget.RecyclerView
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:id="@+id/account_recyclerview">

</androidx.recyclerview.widget.RecyclerView>
```



# MainActivity

## onNavigationItemSelected 수정

```kotlin
override fun onNavigationItemSelected(p0: MenuItem): Boolean {
    when(p0.itemId) {
        
        // ...
        
        R.id.action_account -> {
            var userFragment = UserFragment()
            var bundle = Bundle()
            var uid = FirebaseAuth.getInstance().currentUser?.uid
            bundle.putString("destinationUid", uid)
            userFragment.arguments = bundle
            supportFragmentManager.beginTransaction().replace(R.id.main_content, userFragment).commit()
            return true
        }
    }
    return false;
}
```

action_account 버튼 클릭 시 bundle 객체에 destinationUid를 Key로 하여 현재 사용자의 uid를 저장.

bundle 객체를 fragment의 인자로 전달 후 fragment transition



**Bundle**

String, Int, Long, ... 등 다양한 값을 저장하는 객체로 Actiivty나 Fragment 간 데이터 전달에 사용되는 클래스.

Key, Value 쌍의 Map 구조로 데이터를 저장한다.



# UserFragment

## 전역 변수

```kotlin
var fragmentView: View? = null
var firestore: FirebaseFirestore? = null
var uid: String? = null
var auth: FirebaseAuth? = null
```



## OnCreateView

```kotlin
override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?): View? {
    fragmentView = LayoutInflater.from(activity).inflate(R.layout.fragment_user, container, false)
    uid = arguments?.getString("destinationUid")
    firestore = FirebaseFirestore.getInstance()
    auth = FirebaseAuth.getInstance()

    fragmentView?.account_recyclerview?.adapter = UserFragmentRecyclerViewAdapter()
    fragmentView?.account_recyclerview?.layoutManager = GridLayoutManager(activity!!, 3)
    return fragmentView
}
```

fragment가 생성될 때 데이터를 초기화.

uid의 경우 MainActivity에서 지정한 argument에서 가져와 지정.

데이터를 표시하는 recyclerView에 어탭터와 레이아웃 매니저를 지정한다.

GridLayoutManager는 격자로 데이터를 표시할 수 있고  인자로 현재 context와 colum의 크기를 지정한다.



## UserFragmentRecyclerViewAdapter

```kotlin
inner class UserFragmentRecyclerViewAdapter : RecyclerView.Adapter<RecyclerView.ViewHolder>() {
    var contentDTOs : ArrayList<ContentDTO> = arrayListOf()
    init {
        firestore?.collection("images")?.whereEqualTo("uid", uid)?.addSnapshotListener { querySnapshot, firebaseFirestoreException ->
            if(querySnapshot == null) return@addSnapshotListener

            for (snapshot in querySnapshot.documents) {
                contentDTOs.add(snapshot.toObject(ContentDTO::class.java)!!)
            }
            fragmentView?.account_tv_post_count?.text=contentDTOs.size.toString()
            notifyDataSetChanged()
        }
    }

    override fun onCreateViewHolder(p0: ViewGroup, p1: Int): RecyclerView.ViewHolder {
        var width = resources.displayMetrics.widthPixels / 3

        var imageview = ImageView(p0.context)
        imageview.layoutParams = LinearLayoutCompat.LayoutParams(width, width)
        return CustomViewHolder(imageview)
    }

    inner class CustomViewHolder(var imageview: ImageView) : RecyclerView.ViewHolder(imageview) {

    }

    override fun getItemCount(): Int {
        return contentDTOs.size
    }

    override fun onBindViewHolder(p0: RecyclerView.ViewHolder, p1: Int) {
        var imageview = (p0 as CustomViewHolder).imageview
        Glide.with(p0.itemView.context).load(contentDTOs[p1].imageUrl).apply(RequestOptions().centerCrop()).into(imageview)
    }

}
```

어댑터가 생성될 때 Firebase DB에서 현재 사용자의 uid와 DB의 image 컬렉션에서 uid 필드와 일치하는 쿼리에 대해 snapshotListener 추가.

DB 사용자가 업로드한 데이터가 있다면 document를 ContentDTO 객체로 캐스팅 하여 리스트에 추가.

post count를 업로드한 포스트의 수로 변경.

**onCreateViewHolder** 에서는 격자로 표시하기 위해 표시할 이미지 1/3 크기의 ImageView를 만들고 CustomViewHolder에 담아서 반환.

RecyclerView의 자세한 매커니즘은 이전 포스트 참조.



[commit ac87933](https://github.com/shshksh/instagram_clonecoding/commit/ac87933e6c2eedcf46b625161389253e40c1db35)

