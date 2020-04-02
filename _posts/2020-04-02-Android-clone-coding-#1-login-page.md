---
title: "하울 안드로이드 인스타그램 클론 코딩 #1 로그인 페이지 구현"
excerpt: "인스타그램 클론 코딩. 로그인 페이지 구현"
classes: wide
categories: 
  - clone_coding
tag:
  - android
  - clone_coding
  - howlstagram
read_time: false
last_modified_at: 2020-04-02T22:30:00+09:00
toc: true
---

> 출처 : [인프런-하울의 안드로이드 인스타그램 클론 만들기]([https://www.inflearn.com/course/%EC%9D%B8%EC%8A%A4%ED%83%80%EA%B7%B8%EB%9E%A8%EB%A7%8C%EB%93%A4%EA%B8%B0-%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C/dashboard](https://www.inflearn.com/course/인스타그램만들기-안드로이드/dashboard))



## 로그인 액티비티 생성

액티비티는 일반적으로 화면 1개를 나타낸다. 로그인을 위한 화면을 표시하기 위해 안드로이드 액티비티를 생성한다. 내부 코드는 추후에 수정한다.



## 로그인 액티비티 레이아웃 리소스 생성

### 레이아웃

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".LoginActivity">
    .
    .
    .
</RelativeLayout>
```

**RelativeLayout**: 내부 뷰 들의 위치를 상대적으로 배치할 수 있는 레이아웃.

**match_parent**: 너비 또는 높이를 부모 뷰 그룹에 맞추어 최대한 확장한다.

**wrap_content**: 너비 또는 높이를 자신에 속한 뷰를 감쌀 수 있을 만큼만 적용한다.

**tools:context**: 레이아웃을 대상으로 레이아웃 미리보기 등에 사용된다. ([참고-안드로이드 개발자 문서](https://developer.android.com/studio/write/tool-attributes.html))



### 인스타그램 로고

```xml
<ImageView
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_above="@+id/signin_layout"
    android:layout_alignParentTop="true"
    android:src="@drawable/logo_title" />
```

로그인 페이지에서 인스타그램 로고를 담고있는 **이미지 뷰**.

**android:layout_above**: RelativeLayout의 속성으로 속한 뷰의 위치를 주어진 id가 나타내는 뷰 위에 배치한다.

**android:layout_alignParentTop**: RelativeLayout의 속성. 값을 true로 설정하면 현재의 뷰의 상단 모서리를 부모의 상단 모서리에 맞춘다.

**android:src**: 이미지 뷰가 적용할 리소스를 설정한다. @로 res 내부 drawble 디렉토리를 참조하여 logo_title을 가져올 수 있다.



### 배치될 뷰를 포함하는 레이아웃

```xml
<LinearLayout
    android:id="@+id/signin_layout"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_alignParentBottom="true"
    android:orientation="vertical">
    .
    .
    .
    .
</LinearLayout>
```

**LinearLayout**: 내부에 뷰를 수직 또는 수평 방향으로 배치할 수 있는 레이아웃.



### 이메일와 패스워드 입력 창

```xml
<android.support.design.widget.TextInputLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginLeft="20dp"
    android:layout_marginRight="20dp">

    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="@string/email" />
</android.support.design.widget.TextInputLayout>
```

```xml
<android.support.design.widget.TextInputLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginLeft="20dp"
    android:layout_marginRight="20dp">

    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="@string/password" />
</android.support.design.widget.TextInputLayout>
```

**TextInputLayout**: EditText에 입력된 텍스트를 기반으로 뷰가 동적으로 반응하기 위해 고안된 레이아웃. 사용 시 힌트, 글자수 카운트, 에러 메시지 출력 등의 기능을 사용할 수 있다.

**margin**: 부모 뷰 그룹으로 부터 설정하는 크기만큼 여백을 둔다. 반대로 내부의 요소 사이 빈 공간을 두는 **padding**도 있다.

**EditText**: 텍스트 입력 및 수정이 가능한 뷰.

**hint**: TextInputLayout의 속성으로 텍스트가 입력 되기 전 반투명한 텍스트를 보여준다. 입력 받을 텍스트를 위한 가이드라인 등을 위해 사용할 수 있다.



### 회원가입 버튼

```xml
<Button
    android:layout_width="match_parent"
    android:layout_height="40dp"
    android:layout_marginLeft="20dp"
    android:layout_marginTop="15dp"
    android:layout_marginRight="20dp"
    android:layout_marginBottom="35dp"
    android:text="@string/signin_email"
    android:theme="@style/ButtonStyle" />
```



### 페이스북 로그인 버튼과 구글 로그인 버튼

```xml
<Button
    android:layout_width="match_parent"
    android:layout_height="40dp"
    android:layout_marginLeft="20dp"
    android:layout_marginRight="20dp"
    android:layout_marginBottom="5dp"
    android:background="@drawable/btn_signin_facebook"
    android:text="@string/signin_facebook"
    android:textColor="@color/colorWhite" />
```

```xml
<Button
    android:layout_width="match_parent"
    android:layout_height="40dp"
    android:layout_marginLeft="20dp"
    android:layout_marginRight="20dp"
    android:layout_marginBottom="80dp"
    android:background="@drawable/btn_signin_google"
    android:text="@string/signin_google"
    android:textColor="@color/colorWhite" />
```



## 디자인 라이브러리 디펜던시 추가

```javascript
dependencies {
	.
	.
	.
    implementation 'com.android.support:design:28.0.0'
}
```

build.gradle 파일을 직접 수정하거나 project structure에서 라이브러리를 추가한다.



## 매니페스트 수정

```xml
<activity android:name=".LoginActivity">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />

        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
<activity android:name=".MainActivity">

</activity>
```

LoginActivity가 먼저 나타날 수 있도록 매니페스트 파일을 수정한다.