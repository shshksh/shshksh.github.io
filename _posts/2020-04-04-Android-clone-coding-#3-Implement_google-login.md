---
title: "하울 안드로이드 인스타그램 클론 코딩 #3 구글 로그인"
excerpt: "구글 로그인 API를 사용한 구글 로그인 구현"
classes: wide
categories: 
  - clone_coding
tag:
  - android
  - clone_coding
  - howlstagram
read_time: false
last_modified_at: 2020-04-04T17:50:00+09:00
toc: true
---

> 출처:  [인프런-하울의 안드로이드 인스타그램 클론 만들기]([https://www.inflearn.com/course/%EC%9D%B8%EC%8A%A4%ED%83%80%EA%B7%B8%EB%9E%A8%EB%A7%8C%EB%93%A4%EA%B8%B0-%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C/dashboard](https://www.inflearn.com/course/인스타그램만들기-안드로이드/dashboard))



## 구글 로그인 과정

소셜 로그인 과정은 크게 3단계로 구분된다.

1. 기존의 구글 로그인 플랫폼에서 계정 선택
2. 계정 정보를 파이어베이스에 전달
3. 파이어베이스의 응답



### 라이브러리 추가

구글 로그인 서비스를 사용하기 위해 라이브러리를 추가한다. open module setting에서 play-services-auth를 검색하여 라이브러리 디펜던시를 추가한다.



### Step 1

참고: [Google Sign-In for Android](https://developers.google.com/identity/sign-in/android/sign-in)

```kotlin
var googleSignInClient : GoogleSignInClient? = null
var GOOGLE_LOGIN_CODE = 9001
```

LoginActivity에 구글 로그인을 위한 전역 변수를 추가한다.

**GOOGLE_LOGIN_CODE**: onActivityResult에서 requestCode를 확인하는 정수값



```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    // ...
    google_sign_in_button.setOnClickListener() {
        googleLogin() // 구글 로그인 버튼 클릭 이벤트 핸들러
    }
    var gso = GoogleSignInOptions.Builder(GoogleSignInOptions.DEFAULT_SIGN_IN)
        .requestIdToken(getString(R.string.default_web_client_id))
        .requestEmail()
        .build() // 사용자에게 제공받는 정보에 IdToken, Email을 추가한 옵션을 생성
    googleSignInClient = GoogleSignIn.getClient(this, gso) // 해당 옵션으로 객체 생성
}
```



```kotlin
fun googleLogin() {
    var signInIntent = googleSignInClient?.signInIntent
    startActivityForResult(signInIntent, GOOGLE_LOGIN_CODE)
}
```

googleSignInClient로부터 인텐트를 받아 로그인 액티비티를 실행하는 코드.

**startActivityForResult**: 액티비티를 시작하고 그 결과를 **onActivityResult**로 전달한다. 두 번째 매개변수는 결과를 요청할 떄 확인하는 요청 코드를 보낸다.



### Step 2

```kotlin
override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
    super.onActivityResult(requestCode, resultCode, data)
    if(requestCode == GOOGLE_LOGIN_CODE) {
        var result = Auth.GoogleSignInApi.getSignInResultFromIntent(data)
        if(result.isSuccess) {
            var account = result.signInAccount
            firebaseAuthWithGoogle(account)
        }
    }
}
```

정상적인 요청 코드일 때 결과를 수신하여 계정 정보를 전달한다.



### Step 3

```kotlin
fun firebaseAuthWithGoogle(account : GoogleSignInAccount?) {
    var credential = GoogleAuthProvider.getCredential(account?.idToken, null)
    auth?.signInWithCredential(credential)
        ?.addOnCompleteListener {
                task ->
            if(task.isSuccessful) {
                // Creating a user account
                moveMainPage(task.result!!.user)
            } else {
                //Show the error message
                Toast.makeText(this, task.exception?.message, Toast.LENGTH_LONG).show()
            }
        }
}
```

전달받은 어카운트의 정보로 파이어베이스에 로그인 요청을 하고 정상적으로 완료되면 메인 액티비티로 이동.