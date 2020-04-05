---
title: "하울 안드로이드 인스타그램 클론 코딩 #4 페이스북 로그인"
excerpt: "Facebook API를 활용한 로그인"
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



## 초기 설정

1. 페이스북 라이브러리 추가
2. strings.xml 페이스북 id 수정
3. manifests 인터넷 권한, 메타 데이터 추가



## 페이스북 로그인 구현

### 콜백 매니저 추가

```kotlin
class LoginActivity : AppCompatActivity() {
	// ...
    var callbackManager : CallbackManager? = null
}
```

전역 변수에 콜백 매니저를 추가.

**CallbackManager**: 액티비티나 프래그먼트의 onActivityResult 메서드의 콜백을 관리한다.



### 로그인 버튼 클릭 이벤트 핸들러 지정과 콜백 매니저 초기화

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
	// ...
    facebook_login_button.setOnClickListener {
        facebookLogin()
    }
	// ...
    callbackManager = CallbackManager.Factory.create()
}
```

```kotlin
override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
    super.onActivityResult(requestCode, resultCode, data)
    callbackManager?.onActivityResult(requestCode, resultCode, data)
	// ...
}
```

onActivityResult 메서드에 콜백 매니저의 메서드를 추가.

참고:[facebook developer document](https://developers.facebook.com/docs/reference/android/current/interface/CallbackManager/)



### facebookLogin

```kotlin
fun facebookLogin() {
    LoginManager.getInstance()
        .logInWithReadPermissions(this, Arrays.asList("public_profile", "email"))
    // 로그인 매니저 객체를 받아 사용자 프로필과 이메일을 요청하도록 설정
    
    LoginManager.getInstance()
        .registerCallback(callbackManager, object : FacebookCallback<LoginResult>{
            override fun onSuccess(result: LoginResult?) {
                handleFacebookAccessToken(result?.accessToken)
            }

            override fun onCancel() {
            }

            override fun onError(error: FacebookException?) {
            }
        })
    // 콜백 매니저를 등록
}
```

참고:[facebook developer document](https://developers.facebook.com/docs/reference/android/current/class/LoginManager/)

정상 작동시 결과의 액세스 토큰으로 페이스북 로그인.



### 페이스북 로그인

```kotlin
fun handleFacebookAccessToken(token : AccessToken?) {
    var credential = FacebookAuthProvider.getCredential(token?.token!!)
    auth?.signInWithCredential(credential)
        ?.addOnCompleteListener {
                task ->
            if(task.isSuccessful) {
                // Creating a user account
                moveMainPage(task.result?.user)
            } else {
                //Show the error message
                Toast.makeText(this, task.exception?.message, Toast.LENGTH_LONG).show()
            }
        }
}
```

액세스 토큰으로 **FacebookAuthProvider**에서 credential을 받아 로그인 처리.