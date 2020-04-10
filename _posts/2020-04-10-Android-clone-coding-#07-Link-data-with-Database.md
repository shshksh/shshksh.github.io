---
title: "하울 안드로이드 인스타그램 클론 코딩 #7 DB 연결"
excerpt: "ContentDTO를 만들어 데이터를 Firebase Database에 저장한다."
classes: wide
categories: 
  - clone_coding
tag:
  - android
  - clone_coding
  - howlstagram
read_time: false
last_modified_at: 2020-04-10T23:47:00+09:00
toc: true
---

> 출처: [인프런-하울의 안드로이드 인스타그램 클론 만들기]([https://www.inflearn.com/course/%EC%9D%B8%EC%8A%A4%ED%83%80%EA%B7%B8%EB%9E%A8%EB%A7%8C%EB%93%A4%EA%B8%B0-%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C/dashboard](https://www.inflearn.com/course/인스타그램만들기-안드로이드/dashboard))



## ContentDTO 생성

```kotlin
data class ContentDTO(var explain : String? = null,
                   var imageUrl : String? = null,
                   var uid : String? = null,
                   var userId : String? = null,
                   var timestamp : Long? = null,
                   var favoriteCount : Int? = 0,
                   var favorites : Map<String, Boolean> = HashMap()) {
    data class Comment(var uid : String?= null,
                       var userId: String? = null,
                       var comment : String? = null,
                       var timestamp: Long? = null) {

    }
}
```

**DTO**(Data Transfer Object): 데이터 전송 객체. 서로 다른 시스템(계층)간 데이터 교환을 위해 사용한다. 데이터를 담을 private 필드와 getter, setter로 구성된다. 데이터들을 캡슐화 한 클래스. 이 프로젝트에서는 DB에 저장하기 위해 사용하였다.



## AddPhotoActivity

```kotlin
class AddPhotoActivity : AppCompatActivity() {
    // ...
    var auth : FirebaseAuth? = null
    var firestore : FirebaseFirestore? = null
    
    override fun onCreate(savedInstanceState: Bundle?) {
        // ...
        auth = FirebaseAuth.getInstance()
        firestore = FirebaseFirestore.getInstance()
        // ...
    }
}
```

AddPhotoActivity에 Firebase와 연결하는 **auth**와 DB와 연결하는 **firestore**을 전역 변수로 추가한다.

### contentUpload

```kotlin
fun contentUpload() {
    var timestamp = SimpleDateFormat("yyyyMMdd_HHmmss").format(Date())
    var imageFileName = "IMAGE_" + timestamp + "_.png"

    var storageRef = storage?.reference?.child("images")?.child(imageFileName)

    //Promise method
     storageRef?.putFile(photoUri!!)?.continueWithTask {task: Task<UploadTask.TaskSnapshot> ->
         return@continueWithTask storageRef.downloadUrl
     }?.addOnSuccessListener { uri ->
         var contentDTO = ContentDTO()

         contentDTO.imageUrl =uri.toString()

         contentDTO.uid = auth?.currentUser?.uid

         contentDTO.userId = auth?.currentUser?.email

         contentDTO.explain = addphoto_edit_explain.text.toString()

         contentDTO.timestamp = System.currentTimeMillis()

         firestore?.collection("images")?.document()?.set(contentDTO)

         setResult(Activity.RESULT_OK)

         finish()
     }


    // Callback method
    storageRef?.putFile(photoUri!!)?.addOnCanceledListener {
        storageRef.downloadUrl.addOnSuccessListener { uri ->
            var contentDTO = ContentDTO()

            contentDTO.imageUrl =uri.toString()

            contentDTO.uid = auth?.currentUser?.uid

            contentDTO.userId = auth?.currentUser?.email

            contentDTO.explain = addphoto_edit_explain.text.toString()

            contentDTO.timestamp = System.currentTimeMillis()

            firestore?.collection("images")?.document()?.set(contentDTO)

            setResult(Activity.RESULT_OK)

            finish()
        }
    }
}
```

