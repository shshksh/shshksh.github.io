---
title: "하울 안드로이드 인스타그램 클론 코딩 #5 메인 내비게이션바"
excerpt: "화면을 이동하는 내비게이션바 구현"
classes: wide
categories: 
  - clone_coding
tag:
  - android
  - clone_coding
  - howlstagram
read_time: false
last_modified_at: 2020-04-05T15:30:00+09:00
toc: true
---

> 출처: [인프런-하울의 안드로이드 인스타그램 클론 만들기]([https://www.inflearn.com/course/%EC%9D%B8%EC%8A%A4%ED%83%80%EA%B7%B8%EB%9E%A8%EB%A7%8C%EB%93%A4%EA%B8%B0-%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C/dashboard](https://www.inflearn.com/course/인스타그램만들기-안드로이드/dashboard))



## main.xml

**최 상위 레이아웃**

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">
    ...
</RelativeLayout>
```



**앱 상단 로고**

```xml
<androidx.appcompat.widget.Toolbar
    android:id="@+id/my_toolbar"
    android:layout_width="match_parent"
    android:layout_height="35dp">

    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <ImageView
            android:layout_width="wrap_content"
            android:layout_height="match_parent"
            android:src="@drawable/logo_title" />
    </RelativeLayout>
</androidx.appcompat.widget.Toolbar>
```

RelativeLayout을 넣는 이유가 있을까?



**상단 수평선**

```xml
<LinearLayout
    android:id="@+id/toolbar_division"
    android:layout_width="match_parent"
    android:layout_height="1dp"
    android:layout_below="@+id/my_toolbar"
    android:background="@color/colorDivision"
    android:orientation="horizontal">
</LinearLayout>
```

로고와 메인 화면 사이를 분할하는 수평선 역할.



**메인 화면**

```xml
<FrameLayout
    android:id="@+id/main_content"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_above="@id/nav_division"
    android:layout_below="@id/toolbar_division">
</FrameLayout>
```

레이아웃의 위치는 상단 분할선과 하단 분할선 사이에 위치하도록 지정.



**하단 수평선**

```xml
<LinearLayout
    android:id="@+id/nav_division"
    android:layout_width="match_parent"
    android:layout_height="1dp"
    android:layout_above="@+id/bottom_navigation"
    android:background="@color/colorDivision"
    android:orientation="horizontal">
</LinearLayout>
```

메인 화면과 하단 내비게이션 바를 분할하는 수평선.



**하단 내비게이션바**

```xml
<com.google.android.material.bottomnavigation.BottomNavigationView
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:id="@+id/bottom_navigation"
    android:layout_alignParentBottom="true"
    app:menu="@menu/bottom_navigation_main">
</com.google.android.material.bottomnavigation.BottomNavigationView>
```

레이아웃의 위치를 부모 레이아웃의 하단과 맞닫도록 지정.

**app:menu** 속성으로 내비게이션바의 메뉴를 지정.



### bottom_navigation_main.xml

내비게이션바의 요소를 정의한 파일.

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:id="@+id/action_home"
        android:icon="@drawable/ic_home"
        android:enabled="true"
        android:title="@string/home"/>
    
    <item
        android:id="@+id/action_search"
        android:icon="@drawable/ic_search"
        android:enabled="true"
        android:title="@string/home"/>

    <item
        android:id="@+id/action_add_photo"
        android:icon="@drawable/ic_add_a_photo"
        android:enabled="true"
        android:title="@string/home"/>

    <item
        android:id="@+id/action_favorite_alarm"
        android:icon="@drawable/ic_favorite_border"
        android:enabled="true"
        android:title="@string/home"/>

    <item
        android:id="@+id/action_account"
        android:icon="@drawable/ic_account"
        android:enabled="true"
        android:title="@string/home"/>

</menu>
```

개별 메뉴의 id와 icon을 지정해준다.

**android:title**: 개별 메뉴의 제목을 지정해준다. 이 프로젝트에서는 내비게이션바의 아이콘으로 선택하기 때문에 딱히 지정할 필요가 없어 보인다.

**android:enabled**: 값을 false로 지정 시 메뉴를 선택할 수 없게된다.



## Fragment 생성

**AlarmFragment**, Deatil\*, Grid\*, User\* ...

```kotlin
class AlarmFragment : Fragment() {
    override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle): View? {
        var view = LayoutInflater.from(activity).inflate(R.layout.fragment_alarm, container, false)
        return view
    }
}
```

해당 프래그먼트의 레이아웃을 리턴하도록 onCreateView를 작성.



## MainActivity

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)
    bottom_navigation.setOnNavigationItemSelectedListener(this)
}
```

bottom_navigation의 item이 선택되면 이벤트를 처리하는 핸들러를 지정.



### onNavigationItemSelected

```kotlin
override fun onNavigationItemSelected(p0: MenuItem): Boolean {
    when(p0.itemId) {
        R.id.action_home -> {
            var detailViewFragment = DetailViewFragment()
            supportFragmentManager.beginTransaction().replace(R.id.main_content, detailViewFragment).commit()
            return true
        }
        R.id.action_search -> {
            var gridFragment = GridFragment()
            supportFragmentManager.beginTransaction().replace(R.id.main_content, gridFragment).commit()
            return true
        }
        R.id.action_add_photo -> {
            return true
        }
        R.id.action_favorite_alarm -> {
            var alarmFragment = AlarmFragment()
            supportFragmentManager.beginTransaction().replace(R.id.main_content, alarmFragment).commit()
            return true
        }
        R.id.action_account -> {
            var userFragment = UserFragment()
            supportFragmentManager.beginTransaction().replace(R.id.main_content, userFragment).commit()
            return true
        }
    }
    return false;
}
```

선택되는 Item에 따라 프래그먼트를 생성하고 main_content를 해당 프래그먼트로 대체.