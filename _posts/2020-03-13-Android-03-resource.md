---
title: "안드로이드 리소스"
excerpt: "안드로이드 리소스의 생성과 사용"
classes: wide
categories: 
  - Android
tag:
  - android
  - resource
read_time: false
last_modified_at: 2020-03-13T19:50:00+09:00
toc: true
---

## 안드로이드 리소스

코드 이외의 **리소스**(이미지, 문자열 상수)등은 코드 외부에 유지하는 것이 좋다. 안드로이드는 이와 같은 리소스의 외부화를 문자열이나 색상과 같은 단순한 값에서 이미지나 애니메이션, 테마, UI 레이아웃 등 좀 더 복잡한 리소스에까지 지원하고 있다.

리소스를 외부화 하면 리소스의 유지와 업데이트 등 관리가 수월해 진다.



## 리소스 생성

애플리케이션의 리소스는 프로젝트 계층 구조에서 **res** 폴더에 저장된다. 사용할 수 있는 리소스는 타입에 따라 해당 하위 폴더에 구분 저장된다.

안드로이드 스튜디오에서 새 프로젝트를 생성하면 res 폴더가 자동으로 생성된다. 이 폴더 안에 **values**, **mipmap**, **layout** 리소스에 해당하는 하위 폴더가 생성되며, 각 하위 폴더에 리소스들이 저장된다.

애플리케이션이 빌드되면 이 리소스들은 압축돼 APK에 패키징된다. 빌드 과정에서 **R 클래스 파일**도 생성된다. 이 파일에는 프로젝트에 포함된 각 리소스의 참조가 포함되며, 그럼으로써 코드에서 리소스를 참조하여 사용할 수 있고 디자인 시점에 구문을 검사할 수 있는 장점이 있다. 리소스 파일명은 영문 소문자와 숫자, 점 또는 밑줄로만 구성해야 한다.

### values

지원되는 단순 값은 문자열, 색상, 크기 값, 스타일, 불리언 값, 정수값, 문자열 배열, 입력 배열 등이 있으며 모든 단순 값은 res/values 폴더에 xml파일로 저장된다.

- **문자열**

  문자열 리소스는 **string** 태그로 지정된다.

  ```xml
  <resources>
      <string name="app_name">ProfessionalAndroidSampleCode</string>
  </resources>
  ```

   작은 따옴표, 큰 따옴표, 백슬래시 등은 백슬래시로 이스케이프 처리해야 한다.
   `<b>, <i>, <u>` 등으로 각각 볼드체, 이탤릭체, 밑줄을 적용할 수 있다.
   리소스 문자열은 String.format 메서드의 입력 매개변수로 사용할 수도 있다.

- **색상**

  color 태그를 사용하여 새 색상 리소스를 정의할 수 있다. 색상 값을 지정할 때는 **#** 기호 다음에 순서대로 알파 채널(선택사항), 빨강, 초록, 파랑에 해당하는 값을 지정한다.
   \#RGB, #RRGGBB, #ARGB, #AARRGGBB

  ```xml
  <resources>
      <color name="colorPrimary">#008577</color>
      <color name="colorPrimaryDark">#00574B</color>
      <color name="colorAccent">#D81B60</color>
  </resources>
  ```

- **크기 값**

  크기 값은 테두리나 서체 높이 등 레이아웃 값을 정의할 때 유용하다. 크기 값 리소스를 지정하려면 **dimen** 태그를 사용한다. 이때 다음 단위를 나타내는 식별자를 함께 지정한다.

  - **dp** : 밀도 독립적인 픽셀
  - **sp** : 크기 비례 픽셀
  - px : 화면 픽셀
  - in : 인치
  - pt : 포인트
  - mm : 밀리미터

  이 중 **dp**와 **sp**는 다양한 하드웨어에서 상대적인 크기 값을 정의한다.

  ```xml
  <dimen name="large_font_size">16sp</dimen>
  <dimen name="activity_horizontal_margin">16dp</dimen>
  ```

### 스타일과 테마

스타일 리소스는 애플리케이션에서 뷰가 사용하는 속성 값을 지정함으로써 일관된 시각적 느낌을 유지하게 한다. 스타일을 생성하려면 style 태그를 사용한다. 이 태그에 name 속성을 지정하며, 하나 이상의 item 태그를 함께 사용하기도 한다. 각 item 태그에는 name 속성을 또 지정해 서체 크기나 색상 등을 지정할 수 있다. 

스타일은 style 태그에 parent 속성을 사용해 상속을 지원한다.

```xml
<resources>

    <!-- Base application theme. -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">@color/colorPrimary</item>
        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
        <item name="colorAccent">@color/colorAccent</item>
    </style>

</resources>
```

### drawble

드로어블 리소스는 비트맵, 나인 패치, 크기 비례 벡터 드로어블 등을 나타낸다.

### MipMap

애플리케이션의 아이콘 이미지는 MipMap 폴더 그룹에 저장하는 것이 좋다. 각 이미지는 최대 xxxhdpi 크기까지 해상도별로 저장된다. 

### 레이아웃

레이아웃 리소스는 코드보다 **XML**로 UI 레이아웃을 디자인함으로써 표현 계층과 비즈니스 로직을 분리해 준다. 레이아웃을 사용하면 액티비티나 프래그먼트, 위젯 등 시각적 컴포넌트에 UI를 정의할 수 있다. XML에 정의된 레이아웃은 UI로 인플레이트 돼야 한다. 액티비티 안에서는 setContentView 메서드를 사용하면 되며, 프래그먼트는 프래그먼트의 onCreateView 핸들러로 전달되는 inflator 객체의 inflate 메서드를 사용해 전개한다.



## 코드 내부에서 리소스 사용하기

코드내에서 리소스를 사용할 때는 정적 클래스 **R**을 사용해 리소스에 접근할 수 있다. R클래스는 프로젝트가 빌드될때 자동으로 생성되는 클래스로, 프로젝트에 포함된 모든 리소스를 참조하여 디자인 시점에 구문 검사를 제공한다.

R 클래스에는 사용할 수 있는 각 리소스에 해당하는 정적 서브 클래스, **R.string, R.drawble** 등이 포함된다. 해당 클래스들은 자신과 연동되는 리소스를 변수 형태로 노출한다. 해당 변수 값들은 정수로, 리소스 자체의 인스턴스가 아니라 각 리소스의 리소스 테이블 내 위치를 나타낸다.

리소스 자체의 인스턴스가 필요할 때는 도우미 메소드를 사용하여 리소스 테이블에서 해당 인스턴스를 추출해야 한다. 리소스 테이블은 애플리케이션 안에서 Resources 클래스의 인스턴스로 표현된다.

다음의 코드처럼 getResources 메서드를 사용해 애플리케이션의 Resouces 인스턴스에 접근할 수 있다.

```java
Resources myRes = getResources();
```

Resources 클래스는 사용할 수 있는 각 리소스 타입의 게터를 포함하며, 인스턴스를 생성하려는 리소스의 ID를 전달해 준다. 안드로이드 지원 라이브러리(support.android…, androidx)에는 ResourcesCompat 클래스가 포함되어 있어 안드로이드 구버전과 호환되는 게터 메서드를 제공한다.



## 리소스 내에서 리소스 참조하기

리소스 참조를 다른 XML 리소스에서 속성 값으로 사용할 수도 있다. 한 리소스에서 다른 리소스를 참조하려면 @ 로 접근할 수 있다.

```xml
attribute=”@[package:]resourcetype/resourceidentifier” 
```

```xml
<TextView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Hello World!"
    app:layout_constraintBottom_toBottomOf="parent"
    app:layout_constraintLeft_toLeftOf="parent"
    app:layout_constraintRight_toRightOf="parent"
    app:layout_constraintTop_toTopOf="parent" />
```



## 시스템 리소스 사용하기

안드로이드 프레임워크는 각종 문자열, 이미지, 애니메이션, 스타일, 레이아웃 등 애플리케이션에서 사용할 수 있는 네이티브 리소스를 상당수 제공한다. 시스템 리소스에 코드로 접근하는 방법은 일반 리소스를 사용할 때와 비슷하다. 네이티브 리소스들은 android.R 로 접근할 수 있다.

시스템 리소스에 XML로 접근할 때는 다음과 같이 안드로이드 패키지를 명시해야 한다.

```xml
android:text=”@android:string/httpErrorBadUrl”
```

