---
title: "하울 안드로이드 인스타그램 클론 코딩 #2 파이어베이스 연결"
excerpt: "Firebase를 사용하여 사용자 계정 정보를 관리."
classes: wide
categories: 
  - clone_coding
tag:
  - android
  - clone_coding
  - howlstagram
read_time: false
last_modified_at: 2020-04-03T17:30:00+09:00
toc: true
---

> 출처: [인프런-하울의 안드로이드 인스타그램 클론 만들기]([https://www.inflearn.com/course/%EC%9D%B8%EC%8A%A4%ED%83%80%EA%B7%B8%EB%9E%A8%EB%A7%8C%EB%93%A4%EA%B8%B0-%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C/dashboard](https://www.inflearn.com/course/인스타그램만들기-안드로이드/dashboard))

## 파이어 베이스 연결하기

```kotlin
class LoginActivity : AppCompatActivity() {
    var auth : FirebaseAuth? =null
    .
    .
    .
}
```

파이어베이스를 런타임에 사용할 수 있도록 파이어 베이스 변수를 추가. 코틀린에서 변경 가능한 값은 var(variable)로 선언하며 변경할 수 없는 값은 val(value)로 선언한다. 변수의 자료형은 콜론(:) 뒤에 선언할 수 있으며 ? 는 nullable, 명시적으로 null값이 대입될 수 있다는것을 의미한다.



```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_login)
    auth = FirebaseAuth.getInstance()
    email_login_button.setOnClickListener {
        signinAndSignup()
    }
}
```

**onCreate** 함수에 auth에 파이어베이스 객체를 지정하는 코드와 이메일 로그인 버튼 클릭 리스너를 처리하는 코드를 추가한다.



## 버튼 클릭 리스너

```kotlin
    fun signinAndSignup() {
        auth?.createUserWithEmailAndPassword(email_edittext.text.toString(), password_edittext.text.toString())
            ?.addOnCompleteListener {
                    task ->
                if(task.isSuccessful) {
                    // Creating a user account
                    moveMainPage(task.result!!.user)
                } else if (!task.exception?.message.isNullOrEmpty()) {
                    // Show the error message
                    Toast.makeText(this, task.exception?.message, Toast.LENGTH_LONG).show()
                } else {
                    // Login if you have account
                    signinEmail()
                }
            }
    }
```

파이어베이스 객체로부터 **createUserWithEmailAndPassword** 함수에 입력한 이메일과 패스워드를 전달하여 신규 사용자를 생성. 정상적으로 사용자가 생성되면 MainActivity로 이동, 이미 계정이 있다면 해당 계정으로 로그인하는 함수를 호출, 이외의 경우엔 에러 메시지 출력.

**Toast.makeText**: 사용자에게 짧은 메시지 형식의 정보를 전달하는 팝업을 생성할 수 있다. 첫 번째 매개변수는 현재 프로세스의 Context 정보를 넘겨주고, 두 번째 매개변수는 전달할 메시지, 세 번째 매개변수는 팝업을 띄울 시간을 전달한다.



```kotlin
fun signinEmail() {
    auth?.signInWithEmailAndPassword(email_edittext.text.toString(), password_edittext.text.toString())
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
fun moveMainPage(user: FirebaseUser?) {
    if(user != null) {
        startActivity(Intent(this,MainActivity::class.java))
    }
}
```

**signInWithEmailAndPasswaord** 함수를 사용하여 기존 계정으로 접속할 수 있다. 정상적으로 접속시 메인 액티비티로 이동하며, 이외의 경우엔 에러 메시지를 출력한다.

참고: [Android Firebase document](https://firebase.google.com/docs/auth/android/start)

