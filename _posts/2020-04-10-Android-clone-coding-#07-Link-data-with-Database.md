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
last_modified_at: 2020-04-11T22:43:00+09:00
toc: true
---

> 출처: [인프런-하울의 안드로이드 인스타그램 클론 만들기]([https://www.inflearn.com/course/%EC%9D%B8%EC%8A%A4%ED%83%80%EA%B7%B8%EB%9E%A8%EB%A7%8C%EB%93%A4%EA%B8%B0-%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C/dashboard](https://www.inflearn.com/course/인스타그램만들기-안드로이드/dashboard))



# ContentDTO 생성

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



# AddPhotoActivity

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



## contentUpload

**기존 방식**

```kotlin
fun contentUpload() {
    var timestamp = SimpleDateFormat("yyyyMMdd_HHmmss").format(Date())
    var imageFileName = "IMAGE_" + timestamp + "_.png"

    var storageRef = storage?.reference?.child("images")?.child(imageFileName)
	// Firebase 저장소에서 해당 이미지 파일의 참조를 가져오는 코드
    
    storageRef?.putFile(photoUri!!)?.addOnCanceledListener {
        Toast.makeText(this, getString(R.string.upload_success), Toast.LENGTH_LONG).show()
    }
}
```

StorageReference **getReference**(): Firebase Storage의 루트 경로로 초기화된 StorageReference를 반환한다. 코틀린의 경우 getter와 setter가 자동으로 생성되며 코드상 단순 클래스의 필드에 접근하는것 처럼 보여도 내부적으로는 getter 및 setter를 호출한다.

StorageReference **child**(String pathName): 현재 로케이션의 하위 로케이션의 참조를 반환하는 함수. 매개변수는 상대 경로로 현재 로케이션의 하위 로케이션을 받는다.

UploadTask **putFile**(Uri uri): 현재 스토리지 레퍼런스에 콘텐트 URI를 비동기적으로 업로드하는 함수. 업로드한 콘텐트를 관리하거나 모니터하는데 사용되는 UploadTask를 반환한다.



**변경된 방식**

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

업로드한 파일의 URI를 가져온 후 contentDTO를 생성. 생성된 데이터를 Firebase cloud firestore에 저장한다.

**collection**(String collectionPath): 데이터베이스의 특정 경로의 컬렉션을 참조하는 객체를 반환한다.

**document**(): 현재 컬렉션에 자동으로 생성된 ID를 가지는 새 document의 참조를 반환한다.

**set**(Object data): 현재 DocumentReference의 document를 data로 덮어쓴다. document가 없다면 생성한다.



## Promise와 Callback

### Callback

일반적으로 함수 호출은 호출자(Caller)와 피호출자(Callee)로 나뉘어 호출자가 피호출자를 Call 하여 함수의 기능을 수행한다. 콜백은 반대로 피호출자가 호출자를 부르게 된다.

사용자와 버튼 클릭과 같은 특정한 액션을 취하면 이벤트가 발생한다. 해당 이벤트의 리스너는 이벤트가 발생하는지 항시 대기하며 이벤트가 발생한다면 미리 정의된 콜백 함수를 호출한다.

콜백 함수란 다른 함수의 argument로 사용되는 함수 또는 어떤 이벤트에 의해 호출되는 함수를 말한다.



### Promise

callback과 promise에 대한 대부분의 내용이 자바스크립트와 관련되어 있다. 정확한 이해를 위해서는 추가적인 조사가 필요할듯 싶다.

참고:

- https://developers-kr.googleblog.com/2017/07/keep-your-promises-when-using-cloud.html
- [https://medium.com/@pitzcarraldo/callback-hell-%EA%B3%BC-promise-pattern-471976ffd139](https://medium.com/@pitzcarraldo/callback-hell-과-promise-pattern-471976ffd139)
- 비동기처리에 관한 내용