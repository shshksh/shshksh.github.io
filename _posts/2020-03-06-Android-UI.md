---
title: "안드로이드 UI #2-레이아웃"
excerpt: "안드로이드 레이아웃의 종류와 사용"
# layout: posts
classes: wide
categories: 
  - Android
tag:
  - android
  - UI
read_time: false
last_modified_at: 2020-03-06T20:30:00+09:00
toc: true
---

## 레이아웃

### 안드로이드 SDK 레이아웃의 종류

- **FrameLayout**
  - 레이아웃 매니저의 가장 단순한 형태로 자신의 프레임 안에 자식 뷰 하나하나를 고정한다.
  - 기본 위치는 왼쪽 상단으로 적용되지만, **layout_gravity** 속성으로 변경할 수 있다.
  
- **LinearLayout**
  - 자식 뷰들을 수직이나 수평으로 정렬한다.
  - **layout_weight** 속성으로 공간 안에서 자식 뷰의 상대적 크기를 제어할 수 있다.
  
- **RelativeLayout**
  - 자식 뷰들의 상대적인 위치나 레이아웃 경계를 기준으로 한 자식 뷰의 위치를 정의할 수 있다.
  - 유연하지만 렌더링 부담이 크다.
  
- **ConstraintLayout**
  - RelativeLayout 과 유사하지만 **유연성**과 **효율성**이 더 크다.
  - 자식 뷰들을 그 경계나 다른 자식 뷰 또는 커스텀 가이드라인에 맞춰 늘어놓을 수 있다.
  - ConstaintLayout 은 자체 비주얼 레이아웃 에디터를 제공한다. 이 에디터는 각각의 컴포넌트의 위치를 결정하고 **constraint**(제약, 조건) 을 정의한다.
  
- **참고**
  - **[안드로이드 개발자 가이드 - 레이아웃](https://developer.android.com/guide/topics/ui/declaring-layout.html#CommonLayouts )**
  - 해당 레이아웃의 **LayoutParams** 문서는 레이아웃이 제공하는 메개변수에 대한 정보를 제공한다.
  

<br>

### 레이아웃 정의

1. **XML 형태의 외부 리소스를 사용**
- 레이아웃을 XML로 구현하면 뷰나 프래그먼트, 액티비티의 컨트롤러 코드와 비즈니스 로직에서 **표현 계층이 분리**된다(동작을 제어하는 코드와 UI를 표현하는 코드가 분리된다). 또한 코드 변경 없이 **동적**으로 로드되는 특정 하드웨어 구성을 만들 수 있다.
- XML 작성법
  - XML 코드를 직접 작성
  - 비주얼 레이아웃 에디터 사용

2. **코드 내에서 레이아웃을 정의**
- 코드 내에서 레이아웃 객체를 생성한 후 사용
- setLayoutParams 메서드 또는 addView  메서드로 뷰를 지정할 수 있다.
- *자주 사용되지 않는 레이아웃이나 내용을 동적으로 지정해야 할 때 사용할 수 있지 않을까?*

<br>
### 레이아웃 매개변수

`layout_[params]`라는 특성은 뷰가 들어 있는 뷰 그룹에 대해 뷰의 레이아웃 매개변수를 정의한다.

**너비와 높이**

- **layout_width:** 레이아웃의 너비를 지정
- **layout_height:** 레이아웃의 높이를 지정

너비와 높이는 상수를 지정해 줄 수도 있지만 권장되지 않는다. 대체로 **dp**와 **match_parent**, **warp_content**와 같은 상대적인 특정치를 많이 사용한다.

- **match_parent:** 상위 뷰 그룹이 허용하는 최대한의 크기로 확장한다.
- **wrap_content:** 하위 뷰(위젯)을 포함하는 최소한의 크기로 축소한다.

**레이아웃 생성**

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              android:orientation="vertical">
    <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Button1"/>
    <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Button2"/>
    
</LinearLayout>
```
![sample-layout](/assets/images/sample-image1.png){: .align-center}

<br>


### 레이아웃 최적화

레이아웃의 중첩과 레이아웃에 포함되는 뷰의 수는 애플리케이션의 성능과 반응성에 직접적인 영향을 미친다. 애플리케이션의 퍼포먼스(성능, 반응성)를 위해서는 레이아웃을 단순화 하는것이 중요하다.

**merge와 include**

**merge** 태그는 불필요한 루트 레이아웃을 줄일 수 있다. merge 태그가 포함된 레이아웃이 다른 레이아웃에 추가될 때는 merge 노드가 제거되고, merge 노드의 자식 뷰들이 새 부모 레이아웃에 직접 추가된다.

merge 태그는 include 태그와 함께 사용될 때 더욱 유용하다. **include** 태그는 한 레이아웃의 내용을 다른 레이아웃에 삽입할 때 사용된다.

**뷰의 수를 줄인다.**

애플리캐이션의 실행 속도와 반응성을 극대화하기 위해서는 **80개** 이상의 뷰를 포함하는 레이아웃이 없어야 한다. 이 한도를 초과하면 레이아웃을 인플레이트할 때 걸리는 시간이 크게 증가한다.

뷰 스텁을 사용하여 레이아웃 안에서 인플레이트되는 뷰의 개수를 최소화할 수 있다.

## 위젯

### 안드로이드 위젯 툴박스

- **TextView:** 여러 줄 표시와 문자열 포매팅, 자동 단어 래핑(줄바꿈)을 지원하는 표준 읽기 전용 텍스트 레이블
- **EditText:** 여러 줄 입력, 워드 래핑, 힌트 텍스트를 지원하는 편집 가능한 텍스트 입력 상자
- **ImageView:** 하나의 이미지를 보여주는 뷰
- **Toolbar:** 제목과 공통 액션을 보여주는 뷰. 흔히 액티비티 위에서 메인 앱 바(app bar)로 사용된다.
- **ProgressBar:** 원 또는 수평 막대 형태로 작업 진행률을 보여주는 뷰
- **RecyclerView:** 스크롤이 가능한 컨테이너에 많은 수의 뷰를 담아 보여주는 뷰 그룹. 포함되는 각 뷰를 수직 및 수평 리스트 형태나 그리드 형태로 배치할 수 있는 다양한 레이아웃 매니저를 지원한다.
- **Button:** 상호 작용 방식의 표준 푸시 버튼
- **ImageButton:** 커스텀 배경 이미지를 지정할 수 있는 푸시 버튼
- **CheckBox:** 체크 표시 유무를 나타내는 두 가지 상태 표시용 버튼
- **RadioButton:** 두 가지 상태 표시용 버튼 그룹. 한 번에 하나만 선택할 수 있는 여러 개의 옵션을 보여준다.
- **VideoView**: 액티비티에서 비디오를 더 쉽게 재생할 수 있도록 모든 상태 관리와 화면 구성을 처리한다.
- **ViewPager:** 뷰의 수평적인 스크롤 처리를 구현한다. 사용자가 왼쪽이나 오른쪽으로 뷰를 밀거나 끌어서 서로 다른 뷰를 전환할 수 있게 해준다.

## 리스트와 그리드

방대한 양의 데이터 셋의 데이터들을 표현해야 할 때 각각의 데이터별로 뷰를 추가하는것은 비효율적이다. 안드로이드 지원 라이브러리에 있는 **리사이클러 뷰**는 스크롤이 가능한 뷰 그룹을 제공한다. 리사이클러 뷰는 화면을 벗어나 사용하지 않는 뷰를 **재활용**하는 방식으로 효율성을 높인다.

**리사이클러 뷰와 레이아웃 매니저**

리사이클러 뷰 자체는 각 항목을 화면에 보여주는 방법을 제어하지 않는다. 이 일은 각 항목과 관련된 **RecyclerView.LayoutManager**의 책임이다. 리사이클러 뷰의 항목에는 여러 레이아웃 매니저를 사용할 수 있다.

- **LinearLayoutManager:** 항목들을 하나의 수직 또는 수평 리스트로 배치한다.
- **GridLayoutManager:** 선형 레이아웃 매니저와 비슷하지만 리스트 대신 그리드로 배치한다. 수직으로 배치될 때는 행마다 이때 각 항목의 높이는 같다. 수평 방향의 경우에는 열에 포함된 각 항목의 너비가 같아야 한다.
- **StaggeredGridLayoutManager:** 그리드 레이아웃 매니저와 비슷하지만 그리드 셀마다 너비와 높이가 제각각이다. 각 셀마다 공백을 없앨 수 있다.

**어댑터**

레이아웃 매니저는 표시할 데이터가 생길 때까지는 그다지 유용하지 않다. 표시할 데이터는 **RecyclewVuew.Adapter**가 제공한다. 어댑터의 역할은 크게 두 가지로 나뉜다.

- 보여줄 뷰를 최초 생성하기(적합한 레이아웃을 인플레이트하는 것까지 포함).
- 뷰 요소들을 기본 데이터 소스에 바인딩할 때 사용될 뷰 홀더 만들기.

뷰 홀더는 보여줄 뷰를 저장한다. 또한 [데이터 바인딩]()을 쉽게 하기 위해 메타데이터와 뷰 참조 어댑터가 저장할 수 있도록 한다.

항목의 레이아웃 매니저가 재사용할 미사용 뷰를 갖고 있지 않으면 즉, 한 화면만 채울만큼의 적은 수의 뷰를 가질 경우는 언제든지 어댑터의 **onCreateViewHodler** 메서드가 호출되어 새 **RecyclerView.ViewHolder** 인스턴스를 받는다.