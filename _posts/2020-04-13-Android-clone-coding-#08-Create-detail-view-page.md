---
title: "하울 안드로이드 인스타그램 클론 코딩 #8 상세화면 페이지"
excerpt: "RecyclerView를 이용하여 firebase DB의 데이터를 상세화면 페이지에 표현한다."
classes: wide
categories: 
  - clone_coding
tag:
  - android
  - clone_coding
  - howlstagram
read_time: false
last_modified_at: 2020-04-13T22:30:00+09:00
toc: true
---

> 출처: [인프런-하울의 안드로이드 인스타그램 클론 만들기]([https://www.inflearn.com/course/%EC%9D%B8%EC%8A%A4%ED%83%80%EA%B7%B8%EB%9E%A8%EB%A7%8C%EB%93%A4%EA%B8%B0-%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C/dashboard](https://www.inflearn.com/course/인스타그램만들기-안드로이드/dashboard))



# item_detail.xml 생성

**루트 레이아웃**

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
    ...
</LinearLayout>
```



**상단 바**

```xml
<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="50dp"
    android:gravity="center_vertical">

    <ImageView
        android:id="@+id/detailviewitem_profile_image"
        android:layout_width="35dp"
        android:layout_height="35dp"
        android:layout_margin="7.5dp"
        android:src="@mipmap/ic_launcher" />

    <TextView
        android:id="@+id/detailviewitem_profile_textview"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="User name" />
</LinearLayout>
```



**메인 이미지 뷰**

```xml
<ImageView
    android:id="@+id/detailviewitem_imageview_content"
    android:layout_width="match_parent"
    android:layout_height="250dp"
    android:scaleType="fitXY" />
```

**scaleType**: 지정되는 값에 따라 이미지가 적용되는 범위를 조정할 수 있다. fitXY는 이미지의 가로/세로 비율에 관계없이 뷰에 꽉 차도록 적용된다. 이외에도 center, centerCrop, centerInside, fitCenter, fitStart, fitEnd, matrix 등의 값을 적용할 수 있다.



**좋아요 및 코멘트 아이콘**

```xml
<LinearLayout
    android:layout_marginLeft="8dp"
    android:layout_width="match_parent"
    android:layout_height="50dp"
    android:gravity="center_vertical">

    <ImageView
        android:id="@+id/detailviewitem_favorite_imageview"
        android:layout_width="35dp"
        android:layout_height="35dp"
        android:src="@drawable/ic_favorite_border" />

    <ImageView
        android:id="@+id/detailviewitem_commnet_imageview"
        android:layout_width="35dp"
        android:layout_height="35dp"
        android:src="@drawable/ic_chat_black" />

</LinearLayout>
```



**좋아요 수와 설명 TextView**

```xml
<TextView
    android:id="@+id/detailviewitem_favoritecounter_textview"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="like 0" />

<TextView
    android:id="@+id/detailviewitem_explain_textview"
    android:layout_marginLeft="8dp"
    android:layout_marginRight="8dp"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Explain content" />
```



# fragment_detail.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    >
    <androidx.recyclerview.widget.RecyclerView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:id="@+id/detailviewfragment_recycleview">

    </androidx.recyclerview.widget.RecyclerView>

</LinearLayout>
```

[**recyclerview**]([https://shshksh.github.io/android/Android-UI/#%EB%A6%AC%EC%8A%A4%ED%8A%B8%EC%99%80-%EA%B7%B8%EB%A6%AC%EB%93%9C](https://shshksh.github.io/android/Android-UI/#리스트와-그리드)): 스크롤이 가능한 컨테이너에 많은 수의 뷰를 담아 보여주는 뷰 그룹. 포함되는 각 뷰를 수직 및 수평 리스트 형태나 그리드 형태로 배치할 수 있는 다양한 레이아웃 매니저를 지원한다.



# MainActivity

내비게이션 뷰에서 선택되는 메뉴를 초기화한다.

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
 	// ...
    bottom_navigation.selectedItemId = R.id.action_home
}
```

**selectedItemId**: 내비게이션 뷰의 현재 메뉴를 지정되는 id로 변경한다.



# DetailViewFragment

## 전역변수

```kotlin
var firestore : FirebaseFirestore? = null
```

Firebase DB와 연결하기 위한 전역변수를 추가한다.



## onCreateView

```kotlin
override fun onCreateView( inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?): View? {
    var view = LayoutInflater.from(activity).inflate(R.layout.fragment_detail, container, false)
    firestore = FirebaseFirestore.getInstance()

    view.detailviewfragment_recycleview.adapter = DetailViewRecyclerViewAdapter()
    view.detailviewfragment_recycleview.layoutManager = LinearLayoutManager(activity)

    return view
}
```

Fragment의 **onCreateView**는 xml에 작성된 fragment의 UI View를 인스턴스화하기위해 호출된다.

**inflate**의 의미: 안드로이드에서 xml에 정의된 view를 실제 view 객체로 만든다.

**Parameter**:

- inflater: fragment의 view를 inflate 하는데 사용되는 LayoutInflater 객체.
- container: null이 아닐때, fragment의 UI가 부착되야하는 상위(parent) view.
- savedInstanceState: null이 아닐때, 이 fragment는 주어진 이전의 상태에 따라 재구성된다.

이 함수에선 firebase의 객체를 얻고, fragment의 view를 인스턴스화하여 recycler view의 어댑터와 레이아웃 매니저를 지정한다.

**LinearLayoutManager**(Context context): vertical LinearLayout을 생성한다.



### context

API document

> Interface to global information about an application environment. This is an abstract class whose implementation is provided by the Android system. It allows access to application-specific resources and classes, as well as up-calls for application-level operations such as launching activities, broadcasting and receiving intents, etc.

- 애플리케이션 환경의 global information에 대한 인터페이스
- 추상 클래스로 구체적인 구현은 안드로이드 시스템에서 제공한다.
- 애플리케이션 특정 리소스와 클래스에 대한 접근을 허가하며 액티비티의 실행, 인텐트의 브로드캐스팅과 리시빙과같은 애플리케이션 수준의 명령을 호출한다. 



어떤 Activity 혹은 어떤 application 인가에 대해서 구별하는 정보가 context라고 이해하면 코드를 작성하는데 큰 어려움이 없으실 겁니다.

출처: https://zxcv5500.tistory.com/258 [샤핀의 blog]



Context는 어플리케이션과 관련된 정보에 접근하고자 하거나 어플리케이션과 연관된 시스템 레벨의 함수를 호출하고자 할 때 사용된다.
그런데 안드로이드 시스템에서 어플리케이션 정보를 관리하고 있는 것은 시스템이 아닌, AcitivityManagerService 라는 일종의 또 다른 어플리케이션이다.
따라서 다른 일반적인 플랫폼과는 달리, 안드로이드에서는 어플리케이션과 관련된 정보에 접근하고자 할 때는 AcitivityManagerService를 통해야만 한다.
당연히 정보를 얻고자 하는 어플리케이션이 어떤 어플리케이션인지에 관한 키 값도 필요하다.

즉, 안드로이드 플랫폼 관점에서 Context는 다음과 같은 두 가지 역할을 수행하기 때문에 꼭 필요하다.

- 자신이 어떤 어플리케이션을 나타내고 있는지 알려주는 ID 역할
- ActivityManagerService에 접근할 수 있도록 하는 통로 역할

출처: https://shnoble.tistory.com/57 [노블의 개발이야기]



## DetailViewRecyclerViewAdapter

```kotlin
inner class DetailViewRecyclerViewAdapter : RecyclerView.Adapter<RecyclerView.ViewHolder>() {
    var contentDTOs: ArrayList<ContentDTO> = arrayListOf()
    var contentUidList: ArrayList<String> = arrayListOf()
    // contentDTO와 contentUid를 저장하기 위한 리스트

   // ...
}
```



### init

```kotlin
init {
    firestore?.collection("images")?.orderBy("timestamp")?.addSnapshotListener { querySnapshot, firebaseFirestoreException ->
        contentDTOs.clear()
        contentUidList.clear()
        for (snapshot in querySnapshot!!.documents) {
            var item = snapshot.toObject(ContentDTO::class.java)
            contentDTOs.add(item!!)
            contentUidList.add(snapshot.id)
        }
        notifyDataSetChanged()
    }
}
```

초기화시 firebase db의 이미지들을 시간순으로 정렬한 쿼리를 생성하고 쿼리 내 문서들을 리스트에 저장한다.

DB를 아직 학습하지 않아 이해하기 힘들다.

**notifyDataSetChanged**(): 등록된 옵저버에게 데이터의 변경을 알린다.



### getItemCount

```kotlin
override fun getItemCount(): Int {
    return contentDTOs.size
}
```



### onCreateViewHolder

```kotlin
override fun onCreateViewHolder(p0: ViewGroup, p1: Int): RecyclerView.ViewHolder {
    var view = LayoutInflater.from(p0.context).inflate(R.layout.item_detail,p0,false)
    return CustomViewHolder(view)
}
```



### inner class CustomHolder

```kotlin
inner class CustomViewHolder(view: View) : RecyclerView.ViewHolder(view)
```



### onBindViewHolder

```kotlin
override fun onBindViewHolder(p0: RecyclerView.ViewHolder, p1: Int) {
    var viewholder = (p0 as CustomViewHolder).itemView

    viewholder.detailviewitem_profile_textview.text = contentDTOs!![p1].userId

    Glide.with(p0.itemView.context).load(contentDTOs!![p1].imageUrl).into(viewholder.detailviewitem_imageview_content)

    viewholder.detailviewitem_explain_textview.text = contentDTOs!![p1].explain

    viewholder.detailviewitem_favoritecounter_textview.text = "Likes " + contentDTOs!![p1].favoriteCount

    Glide.with(p0.itemView.context).load(contentDTOs!![p1].imageUrl).into(viewholder.detailviewitem_profile_image)
}
```

**Glide**: 이미지 로딩 라이브러리

> \- Google에서 개발해서 밀고있던 volly이후에 2014년에 공개된 라이브러리입니다.
>
> \- Bump앱을 구글이 인수하면서 bump앱에서 사용하던 이미지 라이브러리를 공개한것이 이 Glide입니다다.
>
> \- 기존의 Picasso에서 사용하는 함수 방식과 거의 비슷합니다(일부 함수를 빼고는 거의 똑같다고 봐도 좋습니다.)
>
> \- 개인적으로 성능이 제일 좋다고 생각하는 라이브러리입니다.(물론 저는 일부 기능에서는 Picasso를 사용합니다.)
>
> \- 다른 이미지 로딩라이브러리에는 없는 썸네일보기, GIF로딩, 동영상 스틸 보기 기능까지 지원합니다.
>
> \- https://github.com/bumptech/glide
>
> 출처: https://gun0912.tistory.com/17 [박상권의 삽질블로그]

Glide를 사용하면 이미지 URL을 로드하며 발생하는 문제를 해결할 수 있다.

**with**: 주어진 context에 로드를 시작한다.

**load**: 주어진 URL을 로드하기 위해 RequestBuilder를 반환한다.

**into**: 로드한 이미지가 들어갈 ImageView를 지정한다.



RecyclerView에서 리스트의 view는 RecyclerView.ViewHolder를 확장한 ViewHolder 객체로 표현된다.  각각의 ViewHolder는 View를 사용하여 단일 항목을 표시한다.

ViewHolder 객체는 RecyclerView.Adapter를 확장한 Adapter에서 관리한다. Adapter는 필요에 따라 ViewHolder를 만들고 ViewHolder를 데이터에 바인딩한다.



레이아웃 매니저는 어댑터의 onCreateViewHolder를 호출한다. 이 함수는 RecyclerView.ViewHolder를 생성하고 뷰 홀더가 콘텐츠를 표시하기 위해 사용하는 뷰를 설정한다.

이후 레이아웃 매니저는 뷰 홀더를 데이터에 바인딩한다. 어댑터의 onBindViewHolder를 호출하고 RecyclerView에 뷰 홀더의 위치를 전달하여 이 작업을 수행한다.