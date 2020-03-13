---
title: "안드로이드 매니페스트"
excerpt: "안드로이드 매니페스트 파일과 서브 태그 목록"
classes: wide
categories: 
  - Android
tag:
  - android
  - manifest
read_time: false
last_modified_at: 2020-03-13T19:48:00+09:00
toc: true
---

## 안드로이드 매니페스트

안드로이드 매니페스트 파일은 애플리케이션의 **구조 및 메타데이터**와 애플리케이션의 **컴포넌트 및 요건**을 정의한다.

구체적으로는 애플리케이션을 구성하는 **액티비티**, **서비스**, **콘텐트 프로바이더**, **브로드캐스트 리시버**에 관련된 노드가 포함되며, **인텐트 필터**와 권한을 지정하여 각 노드들 사이의 상호 작용 방법과 다른 애플리케이션과의 상호 작용 방법을 결정한다.

매니페스트는 루트 태그인 **manifest**와 그 속성인 **package**로 구성된다. package 속성에는 프로젝트 고유의 패키지 이름이 정되며 **xmlns:android**속성에는 매니페스트 파일에 필요한 시스템 속성 몇 가지를 제공한다.

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          package="com.apps.professionalandroidsamplecode">
    <!--...매니페스트 노드...-->
</manifest>
```



## manifest 서브 태그 목록

애플리케이션의 메타데이터(아이콘과 테마)는 매니페스트의 최상위 노드인 **application** 안에 지정된다.  매니페스트의 서브 태그 목록은 다음과 같다.

- **uses-feature**

  애플리케이션이 올바로 작동하기 위한 하드웨어 및 소프트웨어 특징을 지정할 때 사용된다. 지정된 하드웨어가 포함되지 않은 기기에는 애플리케이션이 설치되지 않는다. 하드웨어를 지정해 두고 해당 기기에만 애플리케이션을 설치하게 하려면 이 노드가 필요하다. 

  - 지원되는 하드웨어 목록

    오디오, 블루투스, 카메라, 기기 하드웨어 UI, 지문, 게임 패드, 적외선, 위치, NFC, OpenGL ES 하드웨어, 센서, 전화 통신, 터치스크린, USB, 와이파이, 통신 소프트웨어, 기기 관리 소프트웨어, 미디어 소프트웨어

  - 호환성을 보장하기 위해 일부 권한을 요청해야 할 경우 required 속성을 추가하고 값을 false로 설정하여 해당 요건을 선택적으로 지정할 수 있다.


  ```xml
  <uses-feature android:name="android.hardware.microphone"
                android:required="false" />
  ```

- **supports-screnns**

  해당 노드를 사용하여 애플리케이션에서 사용할 수 있는 화면 해상도를 제한하는 것이 가능하지만, 실제로는 이 방법을 적용하지 않는 것이 좋다.

- **supports-gl-texture**

  GL 텍스쳐 압축 형식으로 압축된 텍스처 애셋을 제공하겠다고 선언한다. 여러 압축 형식을 지원하려면 노드 요소를 그만큼 선언해야 한다.

  ```xml
  <supprots-gl-texture android:name="GL_OES_compressed_ETC1_RGB8_texture" />
  ```

- **uses-permission**

  보안 모델의 일부분은 uses-permission 태그는 애플리케이션에 필요한 사용자 권한을 선언한다. 이 태그로 지정하는 각 권한은 애플리케이션이 설치되기전 또는 애플리케이션이 실행될 때 안드로이드 시스템에서 사용자에게 보여주고 허가를 받는다. 많은 API와 메서드 호출에는 권한이 필요한데, 일반적으로 이런 권한들은 비용이나 보안과 관련된다.

  ```xml
  <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
  ```

- **permission**

  애플리케이션에서 공유되는 컴포넌트들은 지정된 권한에 따라 애플리케이션 내 다른 컴포넌트들에 접근할 수 있다. 기존 플랫폼 권한도 이런 목적으로 사용하거나 필요한 권한을 매니페스트에 직접 정의해 둘 수도 있다. 태그에는 접근 수준과 레이블, 설명에 해당하는 속성이 있다.

  ```xml
  <permission android:name="com.professionalandroid.perm.DETONATE_DEVICE"
              android:protectionLevel="dangerous"
              android:label="Self Destruct"
              android:description="@string/detonate_description">
  </permission>
  ```

- **application**

  이 노드는 하나만 선언할 수 있으며 애플리케이션의 이름, 아이콘, 테마에 관련된 메타데이터가 속성의 형태로 지정된다. 또한 안드로이드 자동 백업을 사용한 데이터 자동 백업 여부와 오른쪽에서 왼쪽으로 처리되는 UI 레이아웃의 지원 여부도 지정될 수 있다.

  application 노드에는 애플리케이션의 컴포넌트들을 지정하는 액티비티, 서비스, 컨텐트 프로바이더, 브로드캐스트 리시버 노드들이 포함된다.

  ```xml
  <application
          android:allowBackup="true"
          android:icon="@mipmap/ic_launcher"
          android:label="@string/app_name"
          android:roundIcon="@mipmap/ic_launcher_round"
          android:supportsRtl="true"
          android:theme="@style/AppTheme">
      <!--...컴포넌트 노드...-->
  </application>
  ```

- **activity**

  애플리케이션 안에서 액티비티마다 하나씩 있어야 한다. 액티비티 클래스 이름을 나타낼 때는 android:name 속성을 사용한다. 이때 제일 처음 실행되는 메인 론칭 액티비티는 물론이고 화면에 보일 수 있는 모든 액티비티를 반드시 포함해야 한다. 매니페스트에 포함되지 않은 액티비티를 시작하려고 하면 런타임 예외가 발생한다. activity 노드는intent-fileter라는 자식태그를 지원한다. 이 태그는 액티비티가 시작될 때 사용될 수 있는 인텐트를 정의한다.

  ```xml
  <activity android:name=".MainActivity">
      <intent-filter>
          <action android:name="android.intent.action.MAIN" />
          <category android:name="android.intent.category.LAUNCHER" />
      </intent-filter>
  </activity>
  ```

- **provider**

  애플리케이션의 콘텐트 프로바이더를 지정한다. 컨텐트 프로바이더는 데이터베이스 접근 및 공유를 관리한다.

- **receiver**

  애플리케이션이 시작되지 않아도 브로드캐스트 리시버를 등록할 수 있다. 브로드캐스트 리시버는 글로벌 이벤트 리스너와 같기 때문에 등록되면 자신이 리스닝하는 것과 일치하는 인텐트가 시스템에서 브로드캐스트될 때마다 실행된다. 특히 receiver 태그로 브로드캐스트 리시버를 매니페스트에 지정하면 등록과 실행 모두 자동으로 처리된다.

  ```xml
  <receiver android:name=".MyIntentReceiver">
  </receiver>
  ```

  