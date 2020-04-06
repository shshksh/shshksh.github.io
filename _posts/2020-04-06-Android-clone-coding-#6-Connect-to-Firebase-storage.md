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



## activity_add_photo 레이아웃 작성

**최상위 레이아웃**

```xml
<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".navigation.AddPhotoActivity">
    
    ...
    
</RelativeLayout>
```



**상단 타이틀 레이아웃**

```xml
<androidx.appcompat.widget.Toolbar
    android:id="@+id/my_toolbar"
    android:layout_width="match_parent"
    android:layout_height="35dp">
    <androidx.appcompat.widget.AppCompatImageView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:src="@drawable/logo_title"/>
</androidx.appcompat.widget.Toolbar>
```

**Toolbar**: 앱 상단의 Appbar를 만들 때 사용하는 뷰. 이전에는 Actionbar를 사용했지만 Toolbar의 경우 하위 호환성을 제공한다.

**AppCompatImageView**: 마찬가지로 ImageView와 하는 일은 동일하지만 하위 호환성을 제공한다.



**상단 레이아웃 구분선**

```xml
<LinearLayout
    android:id="@+id/toolbar_division"
    android:background="@color/colorDivision"
    android:layout_below="@id/my_toolbar"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="1dp">
</LinearLayout>
```

구분선을 LinearLayout을 사용하여 구현했는데 안드로이드에는 View를 사용한 수평 구분선(Horizontal Divider)을 지원한다. 구현 방식의 차이인걸까?



**addphoto_image**

```xml
<ImageView
    android:layout_width="100dp"
    android:layout_height="100dp"
    android:id="@+id/addphoto_image"
    android:layout_margin="8dp"
    android:layout_below="@id/toolbar_division"
    />
```

업로드 하기위해 선택한 이미지가 들어갈 뷰.



**editText**

```xml
<com.google.android.material.textfield.TextInputLayout
    android:id="@+id/editText"
    android:layout_below="@id/toolbar_division"
    android:layout_toRightOf="@+id/addphoto_image"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    >
    <EditText
        android:layout_width="match_parent"
        android:layout_height="100dp"
        android:hint="@string/hint_image_content"
        android:id="@+id/addphoto_edit_explain"
        android:gravity="top"/>
</com.google.android.material.textfield.TextInputLayout>
```

이미지에 대한 소개가 위치할 EditText 뷰. hint를 제공하는 TextInputLayout으로 감쌌다.

addphoto_image와 달리 별다른 margin값을 설정하지 않았는데 margin이 있는것처럼 보인다..?

**android:gravity**: 내부의 콘텐트(이 경우엔 텍스트)의 정렬 위치를 지정하는 속성. layout_gravity의 경우 콘텐트가 아닌 레이아웃의 정렬 위치를 지정할 수 있다.

**android:layout_toRightOf**: 뷰의 왼쪽 가장자리를 주어진 id에 해당하는 뷰의 오른쪽에 맞춘다.



**addphoto_btn_upload**

```xml
<Button
    android:id="@+id/addphoto_btn_upload"
    android:text="Photo_Upload"
    android:layout_toRightOf="@id/addphoto_image"
    android:layout_below="@id/editText"
    android:theme="@style/ButtonStyle"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"/>
```

업로드를 위한 버튼 위젯.





## MainActivity 수정

**onCreate**

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    // ...
    ActivityCompat.requestPermissions(this, arrayOf(Manifest.permission.READ_EXTERNAL_STORAGE), 1)
}
```

**requestPermissions**(Activity activity, String[] permissions, int requestCode): 애플리케이션에 부여할 권한을 요청. 요청할 권한은 매니페스트 파일에 작성되어 있어야 한다.



**onNavigationItemSelected**

```kotlin
override fun onNavigationItemSelected(p0: MenuItem): Boolean {
    when(p0.itemId) {
        // ...
        R.id.action_add_photo -> {
            if(ContextCompat.checkSelfPermission(this, Manifest.permission.READ_EXTERNAL_STORAGE) == PackageManager.PERMISSION_GRANTED){
                startActivity(Intent(this, AddPhotoActivity::class.java))
            }
            return true
        }
       // ...
    }
    return false;
}
```

필요한 권한이 허가되었는지 확인한 후 액티비티를 시작시키는 코드.



## AddPhotoActivity

```kotlin
class AddPhotoActivity : AppCompatActivity() {
    var PICK_IMAGE_FROM_ALBUM = 0
    var storage : FirebaseStorage? = null // Firebase storage를 다루는 변수
    var photoUri : Uri? = null
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_add_photo)

        storage = FirebaseStorage.getInstance() // storage 객체 생성

        var photoPickerIntent = Intent(Intent.ACTION_PICK)
        photoPickerIntent.type = "image/*"
        startActivityForResult(photoPickerIntent, PICK_IMAGE_FROM_ALBUM)

        addphoto_btn_upload.setOnClickListener {
            contentUpload() // 업로드 버튼 클릭 리스너 지정
        }
    }

    override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
        super.onActivityResult(requestCode, resultCode, data)
        if(requestCode == PICK_IMAGE_FROM_ALBUM) {
            if(resultCode == Activity.RESULT_OK) {
                photoUri = data?.data
                addphoto_image.setImageURI(photoUri)
            } else {
                finish()
            }
        }
    }

    fun contentUpload() {
        var timestamp = SimpleDateFormat("yyyyMMdd_HHmmss").format(Date())
        var imageFileName = "IMAGE_" + timestamp + "_.png"

        var storageRef = storage?.reference?.child("images")?.child(imageFileName)

        storageRef?.putFile(photoUri!!)?.addOnCanceledListener {
            Toast.makeText(this, getString(R.string.upload_success), Toast.LENGTH_LONG).show()
        }
    }
}
```

액티비티가 시작되면 먼저 activity_add_photo 레이아웃을 띄운다.

이후 image type 하위 파일을 선택하는 액티비티를 실행. ACTION_PICK은 파일의 uri를 가져온다.

정상적으로 파일을 선택하여 가져왔다면 요청 코드 검증 이후 이미지 뷰에 해당 파일의 uri를 지정하여 보이도록 한다.

업로드 버튼이 클릭되면 현재 시간으로 파일명을 생성.  해당 파일명으로 파이어베이스 스토리지에 파일을 저장하고 취소됐을 때 동작하는 addOnCanceledListner를 지정한다. 

왜 addOnCanceledListener 메시지가 upload_success를 띄우는걸까. addOnCompleteListener로 변경되야할것같다.