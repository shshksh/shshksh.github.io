---
title: "하울 안드로이드 인스타그램 클론 코딩 #13 격자무늬 사진 페이지 만들기"
excerpt: "리사이클러 뷰를 그리드 레이아웃에 담아서 이미지 바인딩"
classes: wide
categories: 
  - clone_coding
tag:
  - android
  - clone_coding
  - howlstagram
read_time: false
last_modified_at: 2020-06-29T19:00:00+09:00
toc: true
---

## fragment_grid.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <androidx.recyclerview.widget.RecyclerView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:id="@+id/gridfragment_recyclerview" />

</LinearLayout>
```

그리드 메뉴 선택시 사진들을 그리드 형태로 보여주기 위해 리사이클러 뷰를 추가.



## GridFragment

```kotlin
class GridFragment : Fragment() {
    var firestore: FirebaseFirestore? = null
    var fragmentView: View? = null

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        fragmentView = LayoutInflater.from(activity).inflate(R.layout.fragment_grid, container, false)
        firestore = FirebaseFirestore.getInstance()
        fragmentView?.gridfragment_recyclerview?.adapter = UserFragmentRecyclerViewAdapter()
        fragmentView?.gridfragment_recyclerview?.layoutManager = GridLayoutManager(activity, 3)

        return fragmentView
    }

    inner class UserFragmentRecyclerViewAdapter : RecyclerView.Adapter<RecyclerView.ViewHolder>() {
        var contentDTOs: ArrayList<ContentDTO> = arrayListOf()

        init {
            firestore?.collection("images")?.addSnapshotListener { querySnapshot, firebaseFirestoreException ->
                    if (querySnapshot == null) return@addSnapshotListener

                    for (snapshot in querySnapshot.documents) {
                        contentDTOs.add(snapshot.toObject(ContentDTO::class.java)!!)
                    }
                    notifyDataSetChanged()
                }
        }

        override fun onCreateViewHolder(p0: ViewGroup, p1: Int): RecyclerView.ViewHolder {
            var width = resources.displayMetrics.widthPixels / 3

            var imageview = ImageView(p0.context)
            imageview.layoutParams = LinearLayoutCompat.LayoutParams(width, width)
            return CustomViewHolder(imageview)
        }

        inner class CustomViewHolder(var imageview: ImageView) :
            RecyclerView.ViewHolder(imageview) {

        }

        override fun getItemCount(): Int {
            return contentDTOs.size
        }

        override fun onBindViewHolder(p0: RecyclerView.ViewHolder, p1: Int) {
            var imageview = (p0 as CustomViewHolder).imageview
            Glide.with(p0.itemView.context).load(contentDTOs[p1].imageUrl)
                .apply(RequestOptions().centerCrop()).into(imageview)
        }

    }

}
```

리사이클러 뷰를 적용하기 위해 GridFragment 수정.

**onCreateView**

- fragment_grid 레이아웃을 인플레이트.
- firestore 객체를 받아 초기화.
- 프래그먼트가 생성될 때 리사이클러 뷰의 어댑터와 레이아웃 매니저를 지정. 사진을 격자 형태로 나타내기 위해 **GridLayoutManager** 를 사용하고 가로 길이를 3으로 지정.



**UserFragmentRecyclerViewAdapter**

- **init**
  - 리사이클러 뷰 어댑터가 생성될 때 firebase storage 에서 게시물 image 를 불러와 contentDTO 배열 초기화.
- **onBindViewHolder**
  - 이미지 리스트에 저장된 이미지를 순서대로 불러와 뷰 홀더에 바인드.



