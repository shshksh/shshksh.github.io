---
title: "하울 안드로이드 인스타그램 클론 코딩 #11 유저 페이지 구현 #2"
excerpt: "다른 사람의 프로필에 접속하여 게시물을 확인하는 기능 추가"
classes: wide
categories: 
  - clone_coding
tag:
  - android
  - clone_coding
  - howlstagram
read_time: false
last_modified_at: 2020-05-03T16:40:15+09:00
toc: true
---

> 출처: [인프런-하울의 안드로이드 인스타그램 클론 만들기]([https://www.inflearn.com/course/%EC%9D%B8%EC%8A%A4%ED%83%80%EA%B7%B8%EB%9E%A8%EB%A7%8C%EB%93%A4%EA%B8%B0-%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C/dashboard](https://www.inflearn.com/course/인스타그램만들기-안드로이드/dashboard))



## LoginActivity

**onStart**

```kotlin
override fun onStart() {
    super.onStart()
    moveMainPage(auth?.currentUser)
}
```

**moveMainPage**

```kotlin
fun moveMainPage(user: FirebaseUser?) {
    if(user != null) {
        startActivity(Intent(this,MainActivity::class.java))
        finish()
    }
}
```

액티비티가 시작될 때 firebase 계정을 확인하여 자동 로그인



## activity_main.xml

뒤로가기 버튼과 유저 ID를 상단 툴바에 추가

```xml
<ImageView
    android:id="@+id/toolbar_btn_back"
    android:layout_width="35dp"
    android:layout_height="35dp"
    android:layout_alignParentLeft="true"
    android:layout_marginLeft="22dp"
    android:src="@drawable/ic_arrow_back" />

<TextView
    android:id="@+id/toolbar_user_name"
    android:layout_width="wrap_content"
    android:layout_height="match_parent"
    android:layout_toRightOf="@id/toolbar_btn_back"
    android:gravity="center"
    android:text="UserID"
    android:textColor="@color/colorNavIcon"
    android:textSize="16sp" />
```



## MainActivity

**onNavigationItemSelected**

```kotlin
override fun onNavigationItemSelected(p0: MenuItem): Boolean {
    setToolbarDefault()
    // ...
}
```

**setToolbarDefault**

```kotlin
fun setToolbarDefault() {
    toolbar_user_name.visibility = View.GONE
    toolbar_btn_back.visibility = View.GONE
    toolbar_title_image.visibility = View.VISIBLE
}
```

내비게이션 메뉴 선택 시 기본적으로 상단 툴바의 뒤로가기 버튼과 ID를 숨기는 함수



## DetailViewFragment

**onBindViewHolder**

```kotlin
override fun onBindViewHolder(p0: RecyclerView.ViewHolder, p1: Int) {
    // ...

    viewholder.detailviewitem_profile_image.setOnClickListener {
        var fragment = UserFragment()
        var bundle = Bundle()
        bundle.putString("destinationUid", contentDTOs[p1].uid)
        bundle.putString("userId", contentDTOs[p1].userId)
        fragment.arguments = bundle
        activity?.supportFragmentManager?.beginTransaction()?.replace(R.id.main_content, fragment)?.commit()
    }
}
```

상세화면 페이지에서 프로필 이미지의 클릭 리스너 추가

번들 객체에 선택한 데이터의 uid와 userId를 담아 UserFragment 호출



## UserFragment

```kotlin
var currentUserId: String? = null
```

현재 표시되는 유저가 계정과 동일한지 확인하는 변수 추가

**onCreateView**

```kotlin
override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?): View? {
    // ...
    currentUserId = auth?.currentUser?.uid

    if (uid == currentUserId) {
        // MyPage
        fragmentView?.account_btn_follow_signout?.text = getString(R.string.signout)
        fragmentView?.account_btn_follow_signout?.setOnClickListener {
            activity?.finish()
            startActivity(Intent(activity, LoginActivity::class.java))
            auth?.signOut()
        }
    } else {
        // OtherUserPage
        fragmentView?.account_btn_follow_signout?.text = getString(R.string.follow)
        var mainActivity = (activity as MainActivity)
        mainActivity?.toolbar_user_name?.text = arguments?.getString("userId")
        mainActivity?.toolbar_btn_back?.setOnClickListener {
            mainActivity.bottom_navigation.selectedItemId = R.id.action_home
        }
        mainActivity?.toolbar_title_image?.visibility = View.GONE
        mainActivity?.toolbar_user_name?.visibility = View.VISIBLE
        mainActivity?.toolbar_btn_back?.visibility = View.VISIBLE
    }
    // ...
}
```

자신의 프로필에 접속시 signout button의 클릭 리스너 지정

signout 시 firebase 계정에서 signout, 현재 액티비티를 종료하고 로그인 액티비티를 시작한다.



다른 유저의 프로필에 접속 시 signout을 follow로 변경, 상단 툴바에 뒤로가기 버튼과 유저의 userId를 표시하고 뒤로가기 버튼 클릭시 DetailViewFragment로 복귀