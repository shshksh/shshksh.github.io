---
title: "안드로이드 인텐트"
excerpt: "안드로이드 인텐트를 활용한 액티비티간 메시지 전달"
classes: wide
categories: 
  - Android
tag:
  - android
  - Intent
read_time: false
last_modified_at: 2020-05-18T19:30:00+09:00
toc: true
---

## Android Intent

**인텐트**: 메시지 전달 메커니즘, 애플리케이션 내부에서와 애플리케이션간,애플리케이션과 시스템 사이에서 사용.

**인텐트의 용도**

- 특정 서비스, 브로드캐스트 리시버, 액티비티, 서브 액티비티를 그 클래스 이름으로 명시적으로 시작할 때
- 특정 데이터를 사용하는 액션을 수행하기 위해 액티비티, 서브 액티비티, 서비스를 시작할 때
- 서브 액티비티에서 정보를 반환할 때
- 이벤트가 발생했다는 것을 브로드캐스트할 때

인텐트는 안드로이드 장치에 설치된 모든 애플리케이션 컴포넌트 간에 정보를 전달하는 데 사용될 수 있다.

일반적으로 명시적 또는 암시적으로 액티비티를 시작시키기 위해 사용된다. 애플리케이션 컴포넌트를 시작시키기 위해 클래스를 직접 로드하는 대신 인텐트를 사용하는 것이 안드로이드 디자인의 핵심 원리다. 또한 인텐트를 사용하여 시스템 전체에 걸쳐 메시지를 브로드캐스트할 수 있으며 이를 브로드캐스트 인텐트라고 한다. 애플리케이션은 브로드캐스트 리시버를 등록하여 해당 브로드캐스트 인텐트를 리스닝하고 반응할 수 있다.

## 인텐트를 사용한 액티비티 시작

**액티비티 생성 메서드**

- **startActivity**: 매개변수로 전달되는 인텐트와 일치하는 액티비티를 찾아 시작시킨다. 전달되는 인텐트는 시작될 특정 **액티비티 클래스를** 명시적으로 지정하거나, 대상 액티비티가 **수행해야 하는 액션을** 포함하는 방식으로 지정할 수 있다.이 메서드로 시작된 액티비티는 끝날 때 어떠한 결과나 데이터를 애플리케이션에 반환하지 않는다.
- **startActivityForResult**: 서브액티비티로부터 결과 데이터를 받아서 처리하기 위해 사용된다.

**명시적 액티비티 시작**

```java
Intent intent = new Intent(MyActivity.this, MyOtherActivity.class);
startActivity(Intent);
```

startActivity로 시작되는 새 액티비티는 현재 실행중인 액티비티를 대체하여 액티비티 스택에 푸시된다. 새 액티비티는 안드로이드 기기에서 뒤로 가기 버튼을 누르거나 finish를 호출할 때 액티비티 스택에서 제거된다. 

메서드를 실행할 때 마다 새 액티비티가 스택에 추가되기 때문에 닫히지 않은 액티비티는 스택에 여전히 존재하게 된다.

**암시적 인텐트**

암시적 인텐트는 특정 액션을 수행할 수 있는 액티비티를 찾아 시작시키도록 시스템에 요청할 때 사용한다. 어떤 애플리케이션이나 액티비탁 시작될지는 정확히 알 수 없다.

암시적 인텐트의 구성은 **수행할 액션**과 **처리될 데이터의 URI**를 지정한다. 이때 Extra를 인텐트에 추가하여 시작할 액티비티에 추가적인 데이터를 전달할 수 있다.

`Intent intent = new Intent(Intent.Action_DIAL, Uri.parse("tel:555-2358"));`

extra는 기본 타입의 값을 인텐트에 추가할 때 사용되는 메커니즘이다. 

`intent.putExtra("TYPE_EXTRA", type_date);`

엑스트라는 Bundle 객체로 인텐트에 저장되며, 인텐트를 통해 호출한 액티비티에서 getExtra 메서드를 호출하여 데이터를 얻을 수 있다.

암시적 인텐트를 사용해 액티비티를 시작하면 안드로이드는 지정된 데이터 타입에 요청된 액션을 가장 잘 수행할 수 있는 액티비티 클래스로 해당 인텐트 처리를 맡긴다. 해당 액션을 다수의 액티비티가 수행할 수 있는 경우에는 사용자가 실행할 액티비티를 선택할 수 있다. 또한 어떤 액티비티가 암시적 인텐트로 실행될 것인지는 액티비티의 매니페스트에 지정되는 intent-filter 에 의해 결정된다.

**액티비티의 결과 반환**

startActicity로 호출되는 액티비티는 호출한 액티비티와 독립적이기 때문에 실행의 결과를 호출한 액티비티에 전달하지 않는다. 

액티비티의 수행 결과를 받아야 하는 경우에는 서브액티비티로 시작시킨다. 서브액티비티의 실행이 끝나면 호출한 부모 액티비티의 onActivityResult 핸들러가 호출된다.

**startActivityForResult**

```java
private static final int PICK_CONTACT_SUBACTIVITY = 2;

pricate void startSubActivityImplicitly() {
	Uri uri = Uri.parse("content://contacts/people");
	Intent intent = new Intent(Intent.ACTION_PICK, uri);
	startActivityForResult(intent, PICK_CONTACT_SUBACTIVITY);
}
```

매개변수에 정수형 요청 코드가 추가된다. requestCode는 onActivityResult에서 서브액티비티를 식별하는데 사용된다.

**onActivityResult**

```java
private static final int SELECT_HORSE = 1;
private static final int SELECT_GUN = 2;

Uri selectedHorse = null;
Uri selectedGun = null;

@Override
public void onActivityResult(int requestCode, int resultCode, Intent data) {
	super.onActivityResult(requestCode, resultCode, data);
	
	switch(requestCode) {
		case (SELECT_HORSE):
			if (resultCode == Activity.RESULT_OK)
				selectedHorse = data.getData();
			break;
		case (SELECT_GUN)
			if (resultCode == Activity.RESULT_OK)
				selectedGun = data.getData();
			break;
	}
}
```

onActivityResult 는 서브액티비티가 종료될 때 호출되며 매개변수로 받은 인텐트로부터 데이터를 넘겨받을 수 있다.

**안드로이드 네이티브 액션**

- **ACTION_DELETE**: 인텐트의 데이터 URI에 지정된 데이터를 삭제할 수 있는 액티비티를 시작시킨다.
- **ACTION_DIAL**: 인텐트의 데이터 URI에 사전 지정된 번호로 전화를 걸도록 전화 애플리케이션이 시작된다.
- **ACTION_EDIT**: 인텐트의 데이터 URI에서 데이터를 편집할 수 있는 액티비티를 요청한다.
- **ACTION_INSERT**: URI에 지정된 커서로 새 항목들을 삽입할 수 있는 액티비티를 시작시킨다.
- **ACTION_PICK**: URI로 지정된 콘텐트 프로바이더로부터 한 항복을 선택할 수 있게 해주는 서브 액티비티를 시작시킨다. 
- **ACTION_SEARCH**: 특정 검색 액티비티를 시작시킬 때 사용된다.
- **ACTION_SENDTO**: URI에 지정된 연락처에 데이터를 전송하는 액티비티를 시작시킨다.
- **ACTION_SEND**: 인텐트에 지정된 데이터를 전송하는 액티비티를 시작시킨다. 수신자는 이 액션으로 시작되는 액티비티에서 선택되어야 한다. 전송되는 데이터의 MINE 타입은 setType을 사용하여 지정한다.
- **ACTION_VIEW**: URI에 제공되는 데이터를 가장 적합한 방법으로 보여줘야 한다는 것을 의미하는 요청. 제공되는 URI 형식에 따라 서로 다른 애플리케이션이 뷰 요청을 처리한다.
- **ACTION_WEB_SEARCH**: SearchManage.QUERY 키로 제공된 쿼리 데이터를 기준으로 웹 검색을 수행하기 위해 웹 브라우저를 실행시킨다.

## 암시적 인텐트를 수신하는 인텐트 필터

인텐트 필터는 액티비티가 인텐트 필터에 지정된 액션을 수행할 수 있다는 것울 선언한다. 인텐트 필터는 매니페스트 파일에서 액티비티 태그 내에 `<intent-filter>` 태그로 선언한다.

**intent-filter 내부 태그**

- **action**: `android:name` 속성을 사용해서 수행할 수 있는 액션의 이름을 지정
- **category**: `android:name` 속성을 사용해서 액션이 수행될 수 있는 상황을 지정
- **data**: 컴포넌트가 수행될 수 있는 데이터 타입을 지정할 수 있다

**intent resolution**

암시적 인텐트가 startActivity로 전달될 때 어떤 액티비티를 시작시킬지 결정하는 과정

1. 안드로이드는 설치된 패키지들로부터 사용할 수 있는 모든 인텐트 필터 정보를 수집하여 리스트를 생성한다.
2. resolve 되는 인텐트와 관련된 액션이나 카테고리와 일치하지 않는 인텐트 필터들이 리스트에서 제거된다.
   - 액션 일치는 지정된 액션을 인텐트 필터가 포함할 때만 이루어진다. 인텐트 필터의 어떤 액션도 인텐트에 지정된 것과 일치하지 않으면 해당 인텐트 필터가 액션 일치에 실패한 것으로 간주된다.
   - 카테고리 일치 검사의 경우, 인텐트 필터는 resolve 하는 인텐트에 정의된 모든 카테고리를 포함해야 한다.
3. 인텐트의 데이터 URI의 각 부분이 인텐트 필터의 data 태그와 비교된다. 인텐트 필터에 스키마, 호스트/권한, 경로, MIME 타입이 지정되어 있으면 이 값들이 인텐트의 URI와 비교된다. 불일치되는 인텐트 필터는 리스트에서 제거된다.
   - MIME 타입: 일치되어야 하는 데이터의 데이터 타입.
4. 액티비티를 암시적으로 시작시킬 때 만일 두 개 이상의 컴포넌트가 3번까지의 인텐트 레졸루션 과정에서 일치되면 그 내역을 사용자에게 보여주고 원하는 컴포넌트를 선택하게 해준다.

