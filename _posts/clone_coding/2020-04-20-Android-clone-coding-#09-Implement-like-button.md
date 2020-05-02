---
title: "하울 안드로이드 인스타그램 클론 코딩 #9 좋아요 버튼 구현"
excerpt: "좋아요 버튼 클릭시 동작을 구현"
classes: wide
categories: 
  - clone_coding
tag:
  - android
  - clone_coding
  - howlstagram
read_time: false
last_modified_at: 2020-04-20T22:18:00+09:00
toc: true
---

> 출처: [인프런-하울의 안드로이드 인스타그램 클론 만들기]([https://www.inflearn.com/course/%EC%9D%B8%EC%8A%A4%ED%83%80%EA%B7%B8%EB%9E%A8%EB%A7%8C%EB%93%A4%EA%B8%B0-%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C/dashboard](https://www.inflearn.com/course/인스타그램만들기-안드로이드/dashboard))



# DetailViewFragment

## favoriteEvent

```kotlin
fun favoriteEvent(position : Int) {
    var tsDoc = firestore?.collection("images")?.document(contentUidList[position])
    firestore?.runTransaction{ transaction ->

        var contentDTO = transaction.get(tsDoc!!).toObject(ContentDTO::class.java)

        if(contentDTO!!. favorites.containsKey(uid)) {
            contentDTO?.favoriteCount = contentDTO?.favoriteCount?.minus(1)
            contentDTO?.favorites.remove(uid)
        } else {
            contentDTO?.favoriteCount = contentDTO?.favoriteCount?.plus(1)
            contentDTO?.favorites[uid!!] = true
        }
        transaction.set(tsDoc, contentDTO)
    }
}
```

**position**: 좋아요 버튼이 들어있는 뷰의 위치

현재 버튼을 누른 view의 document를 **firebase**에서 가져와 **tsDoc**에 저장한다. 이후 firebase에 데이터를 쓰기 위해 트랜잭션을 시작한다. 

**transaction**

- firebase에서 이전에 저장한 document를 받아 contentDTO 형태로 저장한다. 
- contentDTO객체의 favorites: MutableHashMap 가 현재 사용자의 uid 주소를 키로 가지고 있다면 좋아요 카운트를 마이너스 하고 맵에서 uid 키를 지운다.
- 반대로 현재 사용자의 uid를 키로 가지고 있지 않다면 좋아요 개수를 하나 올리고 맵에 데이터를 추가한다.
- transaction.set 함수로 tsDocd에 변경된 contentDTO를 overwrite한다.



## onBindViewHolder

```kotlin
override fun onBindViewHolder(p0: RecyclerView.ViewHolder, p1: Int) {
    // ...
    
    viewholder.detailviewitem_favorite_imageview.setOnClickListener {
        favoriteEvent(p1)
    }
    
    if(contentDTOs!![p1].favorites.containsKey(uid)) {
        viewholder.detailviewitem_favorite_imageview.setImageResource(R.drawable.ic_favorite)
    } else {
        viewholder.detailviewitem_favorite_imageview.setImageResource(R.drawable.ic_favorite_border)
    }
}
```

좋아요 버튼 클릭시 favoriteEvent를 호출하는 클릭리스너를 등록한다.

이후 현재 사용자가 좋아요 버튼을 누른 여부에 따라 좋아요 이미지를 변경한다.