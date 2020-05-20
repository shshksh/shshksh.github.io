---
title: "안드로이드 브로드캐스트 리시버"
excerpt: "브로드캐스트 리시버를 통한 이벤트 리스닝"
classes: wide
categories: 
  - Android
tag:
  - android
  - Broadcast receiver
read_time: false
last_modified_at: 2020-05-20T16:30:00+09:00
toc: true
---

## 브로드캐스트 리시버

인텐트는 애플리케이션 컴포넌트를 시작시키는 용도 이외에도 컴포넌트 간에 메시지를 브로드캐스트할 때도 인텐트를 사용할 수 있다. 메시지 브로드캐스트에는 `sendBroadcast` 메서드를 사용한다.

인텐트로 액티비티를 시작시킬때와 마찬가지로 명시적이나 암시적으로 브로드캐스트할 수 있다. 명시적 브로드캐스트는 시작시키고자 하는 브로드캐스트 리시버의 클래스를 지정하고 암시적 브로드캐스트는 인텐트의 액션과 데이터, 카테고리를 지정하여 이를 통해 실행될 브로드캐스트 리시버가 결정된다.

```java
Intent explicitIntent = new Intent(this, MyBroadcastReceiver.class);
explicitIntent.putExtra(...);
explicitIntent.putExtra(...);
sendBroadcast(explicitIntent);

Intent implicitIntent = new Intent(Receiver.ACTION);
implicitIntent.putExtra(...);
implicitIntent.putExtra(...);
sendBroadcast(implicitIntent);
```

**브로드캐스트 인텐트 리스닝**

브로드캐스트된 인텐트를 수신하기 위해서는 브로드캐스트 리시버가 등록되어야 한다. 리시버는 코드상에서 등록하거나 매니페스트에 등록할 수 있다. 새 브로드캐스트 리시버를 생성하려면 `BroadcastReceiver` 클래스의 서브 클래스를 생성하고 `onReceive` 메서드를 오버라이드해야 한다.

```java
public class MtBroadcastReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        // 수신한 인텐트에 대한 응답
    }
}
```

**리시버 등록/해제**

리시버의 등록은 `registerReceiver` 메서드를 이용하여 등록한다. 메서드의 매개변수로 리시버 객체와 리시버가 수신할 암시적 브로드캐스트 인텐트와 연관된 액션을 정의하는 `IntentFileter` 객체를 지정한다.

등록된 리시버의 해제는 `unregisterReceiver` 메서드로 등록을 해제한다.

**매니페스트 리시버**

애플리케이션 매니페스트에 정적으로 등록된 브로드캐스트 리시버는 항상 활성 상태가 된다. 따라서 애플리케이션이 중단되었거나 아직 시작되지 않았을 땨도 브로드캐스트 인텐트를 받는다. 따라서 수신대기중인 인텐트가 브로드캐스트되면 애플리케이션이 자동으로 시작된다.

매니페스트 리시버는 `application` 노드에 `receiver` 태그를 추가하여 등록시킬 수 있다.

`<receiver android:name=".MyBroadcastReceiver/>"`

매니페스트 리시버는 애플리케이션이 종료된 후에도 브로드캐스트 이벤트 발생시 응답하게 되므로 애플리케이션과 연관된 리소스가 소비되는 위험을 초래할 수 있다. 안드로이드 8.0 부터는 이러한 위험을 최소화하기 위해 암시적 브로드캐스트 리시버를 매니페스트에 등록할 수 없다.

**런타임상의 매니페스트 리시버 관리**

패키니 매니저를 사용하여 애플리케이션의 매니페스트 리시버를 런타임상에서 활성화/비활성화가 가능하다. `setComponentEnabledSetting` 메서드는 모든 애플리케이션 컴포넌트의 활성화/비활성화에 사용할 수 있다. 

```java
ComponentName myReceiverName = new ComponentName(this, Receiver.class);
        PackageManager pm = getPackageManager();
        
        pm.setComponentEnabledSetting(myReceiverName, PackageManager.COMPONENT_ENABLED_STATE_ENABLED, PackageManager.DONT_KILL_APP);
        
        pm.setComponentEnabledSetting(myReceiverName, PackageManager.COMPONENT_ENABLED_STATE_DISABLED, PackageManager.DONT_KILL_APP);
```

