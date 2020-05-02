---
title: "안드로이드 그래들"
excerpt: "안드로이드 그래들 파일의 이해"
classes: wide
categories: 
  - Android
tag:
  - android
  - gradle
read_time: false
last_modified_at: 2020-03-13T19:49:00+09:00
toc: true
---

## 안드로이드 그래들

안드로이드 프로젝트에는 **빌드 구성 정보**를 정의하기 위한 일련의 **그래들** 빌드 파일이 포함된다.



## 그래들 파일 목록

- **settings.gradle**(project)

  애플리케이션을 빌드할 때 포함될 모듈들을 정의한다. 기본적으로 하나의 애플리케이션 모듈이 포함된다. 프로젝트가 여러 개의 모듈을 포함하도록 커지면 관련 모듈을 여기에 추가해야 한다.

  ```java
  include ':app'
  rootProject.name='ProfessionalAndroidSampleCode'
  ```

- **build.gradle**(project)

  그래들 자체의 저장소 및 의존성을 지정한다. 또한 모듈들에 공통되는 저장소 및 의존성도 함께 지정한다.

  - **buildscript**

    애플리케이션이 아니라 그래들이 사용하는 저장소와 의존성을 지정한다. 기본 dependencies 블록에는 그래들에서 안드로이드 애플리케이션의 모듈들을 빌드하는 데 필수인 그래들용 안드로이드 플러그인이 포함되고, repositories 블록에는 그래들이 이를 찾기 위해 이용하는 저장소로 Jcenter 와 google의 예비 설정이 포함된다.

    ```java
    buildscript {
        repositories {
            google()
            jcenter()
            
        }
        dependencies {
            classpath 'com.android.tools.build:gradle:3.5.3'
            
            // NOTE: Do not place your application dependencies here; they belong
            // in the individual module build.gradle files
        }
    }
    ```

  - **allprojects**

    프로젝트의 모든 모듈이 사용할 저장소와 의존성을 지정할 때 사용된다. 모듈이 하나인 프로젝트의 경우에는 그 의존성을 모듈 차원의 build.gradle 파일에 포함하는 것이 일반적이다.

    ```java
    allprojects {
        repositories {
            google()
            jcenter()
        }
    }
    ```

  - **taskclean**

    프로젝트 빌드 폴더에 담긴 내용물을 삭제한다.

    ```java
    task clean(type: Delete) {
        delete rootProject.buildDir
    }
    ```

- **build.gradle**(module)

  애플리케이션의 빌드 설정을 구성한다. 여기에는 의존성, 최소 및 대상 플랫폼 버전, 애플리케이션의 버전 정보, 빌드 유형 및 제품 버전 등도 포함된다.

  빌드 설정의 첫 행은 그래들용 안드로이드 플러그인을 이 빌드에 적용한다.

  **android 블록**의 최상위 레벨에는 안드로이드 애플리케이션 구성 옵션, 애플리케이션을 컴파일할 SDK 버전 등을 지정한다. 이 값은 새 SDK 릴리즈를 다운로드하여 사용할 때 업데이트해야 한다.

  - **defaultConfig**: 모든 제품 플레이버에 공유되는 기본 설정을 지정한다.

    - **applicationId**: 발행 및 배포용으로 빌드된 APK를 식별하기 위한 고유 패키지 이름을 제공한다. 기본적으로 그리고 필요에 의해 매니페스트 안에 정의된 패키지 이름과 같은 이름을 사용해야 한다.

    - **minSdkVersion**: 애플리케이션과 호환되는 안드로이드 플랫폼의 최소 버전을 설정한다. 즉 애플리케이션이 설치 및 실행될 가장 낮은 안드로이드 플랫폼 버전을 나타낸다. 최소 버전을 지정하지 않으면 기본값인 1이 적용돼 모든 기기에서 설치가 가능하지만 사용할 수 없는 API가 호출될 경우, 실행이 종료된다.

    - **targetSdkVersion**: 애플리케이션 개발 및 테스트에 사용하는 안드로이드 플랫폼 버전을 지정한다. 최신 버전일수록 개선점이 많으므로 가장 최신 안드로이드 버전을 지정하는 것이 좋다. minSdkVersion이 16이고 targetSdkVersion이 28이라면 16부터 28까지의 기기에서 애플리케이션이 설치 및 실행될 수 있다는 것을 의미한다.

    - **versionCode**: 현재 애플리케이션의 버전을 정수로 정의한다. 이 정수는 애플리케이션을 릴리즈할 때마다 증가된다.

    - **versionName**: 사용자에게 보여줄 버전 식별자를 지정한다.

    - **tertInstrumentationRunner**: 애플리케이션 테스트에 사용할 테스트 러너를 지정한다. 기본적으로는 안드로이드 지원 라이브러리의 AndroidJuitRunner 가 제공된다. 

  - **buildTypes**: 각기 다른 여러 빌드 유형, 일반적으로는 디버그와 릴리스 등을 정의할 때 사용된다. 새 모듈을 만들면 안드로이드 스튜디오가 자동으로 릴리스 빌드 유형을 만들어 주고, 대개는 이대로 사용된다.

  - **productFlavors**와 **flavorDimensions**: 기본으로 포함되지 않고 **선택적으로** 지정하는 블록이다. 이 블록들을 사용하면 defaultConfig 블록에 정의된 값들을 오버라이드하여 같은 소스 코드를 사용하는 애플리케이션의 서로 다른 버전(플레이버)을 지원할 수 있다. 독립적인 배포와 설치를 할 수 있도록 각 플레이버에는 자신의 고유한 applicationId를 지정해야 한다.

    flavorDimensions는 최종 빌드 변형 버전을 생성하기 위해 결합될 수 있는 플레이버의 그룹을 만들 수 있다. 따라서 여러 디멘션에 따른 빌드 변경을 지정할 수 있다. 예를 들어, 무료 빌드나 유료 빌드를 기준으로 하는 변경, 최소 API 레벨을 기준을 하는 변경 등이 있다.

    애플리케이션을 빌드할 때 그래들은 각 디멘션과 빌드 유형 구성에 따라 제품 플레이버를 결합하여 최종 빌드 변형 버전을 생성한다. 이때 그래들은 같은 플레이버 디멘션에 속하는 제품 플레이버는 결합하지 않는다.

  - **splits**: 안드로이드에서 지원되는 화면 밀도나 ABI 각각에 대해 코드와 리소스만 포함하는 서로 다른 APK 빌드를 구성하기 위해 splits 블록을 사용할 수 있다(선택사항).

  - **dependencies**: 애플리케이션을 빌드하는 데 필요한 의존성을 지정한다.
     기본적으로 새 프로젝트에는 로컬 바이너리 의존성이 포함된다. apps/libs 폴더에 있는 모든 JAR 파일 그리고 안드로이드 지원 라이브러리와 Junit의 원격 바이너리 의존성, 안드로이드 에스프레소 테스팅 라이브러리 의존성을 포함시키라고 그래들에게 알려주는 것이 로컬 바이너리 의존성이다.

  

대부분의 경우 프로젝트 영역의 그래들 파일은 건드리지 않아도 되지만 모듈 차원의 그래들 빌드 파일들은 지속적인 변경이 필요하다.

```java
apply plugin: 'com.android.application'

android {
    compileSdkVersion 28
    buildToolsVersion "29.0.2"
    defaultConfig {
        applicationId "com.apps.professionalandroidsamplecode"
        minSdkVersion 16
        targetSdkVersion 28
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 					'proguard-rules.pro'
        }
    }
}

dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'androidx.appcompat:appcompat:1.0.2'
    implementation 'androidx.constraintlayout:constraintlayout:1.1.3'
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'androidx.test.ext:junit:1.1.0'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.1.1'
}
```