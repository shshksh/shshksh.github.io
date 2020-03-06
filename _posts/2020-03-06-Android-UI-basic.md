---
title: "안드로이드 UI #1-기초"
excerpt: "안드로이드 UI의 기초"
layout: posts
categories: 
  - Android
read_time: false
last_modified_at: 2020-03-06T20:20:00+09:00
# toc: true
# toc_sticky: true
---

## 안드로이드 UI의 기초

- 안드로이드의 모든 시각적 컴포넌트의 뿌리는 **View** 클래스.
- **ViewGroup** 클래스는 뷰의 확장판으로 자식 뷰의 추가를 지원. 뷰 그룹은 자식 뷰의 크기와 위치를 결정한다. 자식 뷰의 배치를 주로 담당하는 뷰 그룹을 **레이아웃** 이라고 한다.
- 뷰의 생성과 배치는 코드상에서도 가능하지만, **XML** 레이아웃 리소스를 사용하여 구성하는 방법이 더욱 유연한 접근성을 제공한다.

![view-viewgroup](/assets/images/view-viewgroup.png){: .align-center}

(**출처:** [안드로이드 개발자 가이드](https://developer.android.com/guide/topics/ui/declaring-layout.html#layout-params))

### 액티비티에 레이아웃 배치하기

- 새 액티비티는 **onCreate** 메서드의 호출과 함께 빈 화면으로 시작하며, 새 UI들은 그 위에 배치된다.

- **onCreate** 를 오버라이드할 때 **setContentView** 를 사용하여 액티비티에 UI를 배치할 수 있다.

  ```java
  @Override
  public void onCreate(Bundle savedInstanceState) {
  	super.onCreate(savedInstanceState);
  	setContentView(R.layout.main);
  	// 액티비티에 main 레이아웃을 배치
  }
  ```

- 레이아웃이 설정되면 **findViewById** 메서드를 사용하여 레이아웃 내부 뷰의 참조를 얻을 수 있다.

  ```java
  TextView textView = findViewById(R.id.myTextView);
  // 레이아웃 내부 myTextView의 참조를 반환
  ```

- **Fragment** 를 사용해 액티비티의 UI 부분을 캡슐화할 때는 액티비티의 onCreate 핸들러 안에서 설정된 뷰가 각 프래그먼트의 상대적 위치를 나타내는 레이아웃이 된다. 또한 각 프래그먼트에 사용된 UI는 자신의 레이아웃에서 정의되고 프래그먼트 자체에서 인플레이트되며, 거의 모든 경우 해당 프래그먼트에서만 처리돼야 한다.