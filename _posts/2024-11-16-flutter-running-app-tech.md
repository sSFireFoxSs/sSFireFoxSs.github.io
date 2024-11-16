---
title: "[Flutter] 러닝 앱 만들면서 쓴 기술 정리 - 지도, 위치, 카카오 로그인, Firestore"
date: 2024-11-16 21:40:00 +0900
categories: [Flutter, 정리]
tags: [flutter, google-maps, geolocator, kakao, firebase, firestore]
---

졸업작품(러닝 앱)을 만들면서 쓴 것 중에 **다음에도 써먹을 만한 것만** 정리.
설정할 때마다 다시 찾아보느라 시간을 많이 써서, 순서대로 따라 하면 되게 적어둔다.

## 1. 구글 지도 띄우기 (google_maps_flutter)

### API 키 등록

Google Cloud Console에서 **Maps SDK for Android / iOS**를 켜고 API 키를 발급받는다.

```xml
<!-- android/app/src/main/AndroidManifest.xml (application 태그 안) -->
<meta-data
    android:name="com.google.android.geo.API_KEY"
    android:value="발급받은 키"/>
```

```swift
// ios/Runner/AppDelegate.swift
import GoogleMaps

GMSServices.provideAPIKey("발급받은 키")
```

> API 키를 깃허브에 그대로 올리지 않기. 키 제한(앱 패키지명) 걸어두기.
{: .prompt-warning }

### 지도 위젯

```dart
GoogleMap(
  initialCameraPosition: CameraPosition(target: _currentPosition!, zoom: 17),
  myLocationEnabled: true,
  polylines: _polylines,                        // 경로 선
  onMapCreated: (controller) => _mapController = controller,
)
```

현재 위치를 받기 전에 지도를 그리면 엉뚱한 곳(또는 빈 화면)이 나와서,
**위치를 먼저 받고 → 지도를 그리는** 순서로 했다.

```dart
_currentPosition == null
    ? const Center(child: CircularProgressIndicator())
    : GoogleMap(...)
```

## 2. 위치 권한 처리 (geolocator)

### 플랫폼 설정

```xml
<!-- AndroidManifest.xml -->
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
```

```xml
<!-- ios/Runner/Info.plist -->
<key>NSLocationWhenInUseUsageDescription</key>
<string>러닝 경로를 기록하기 위해 위치 정보가 필요합니다.</string>
```

### 경우별로 나눠서 처리

지도가 안 뜨는 원인이 하나가 아니었다. **원인마다 사용자가 해야 할 일이 달라서** 나눠서 안내했다.

| 상황 | 사용자가 할 일 |
| --- | --- |
| 위치 서비스 꺼짐 | 휴대폰 설정에서 위치 켜기 |
| 권한 거부 | 앱에서 다시 요청 |
| 영구 거부 | 앱 설정 화면으로 보내기 |

```dart
Future<Position?> getPosition() async {
  if (!await Geolocator.isLocationServiceEnabled()) {
    showMessage('위치 서비스가 꺼져 있습니다.');
    return null;
  }

  var permission = await Geolocator.checkPermission();
  if (permission == LocationPermission.denied) {
    permission = await Geolocator.requestPermission();   // 한 번 더 요청
    if (permission == LocationPermission.denied) {
      showMessage('위치 권한이 거부되었습니다.');
      return null;
    }
  }

  if (permission == LocationPermission.deniedForever) {
    showMessage('설정에서 위치 권한을 허용해주세요.');
    await Geolocator.openAppSettings();                  // 설정 화면으로
    return null;
  }

  return Geolocator.getCurrentPosition(desiredAccuracy: LocationAccuracy.high);
}
```

## 3. 달린 경로 그리기 + 거리 계산

![러닝 화면](/assets/img/posts/rwf/rwf-running.png){: width="260" }
_실제 앱 러닝 화면 - 5m마다 쌓은 좌표로 그린 경로_

처음엔 좌표가 드문드문 들어와서 **굽은 길이 직선으로** 그려졌다.
`distanceFilter`로 **5m 움직일 때마다** 좌표를 받도록 바꾸니 실제 길을 따라 그려졌다.

```dart
_sub = Geolocator.getPositionStream(
  locationSettings: const LocationSettings(
    accuracy: LocationAccuracy.best,
    distanceFilter: 5,                 // 5m마다 업데이트
  ),
).listen((position) {
  if (_lastPosition != null) {
    final meters = Geolocator.distanceBetween(
      _lastPosition!.latitude, _lastPosition!.longitude,
      position.latitude, position.longitude,
    );
    setState(() {
      _distanceKm += meters / 1000;
      _route.add(LatLng(position.latitude, position.longitude));
      _polylines
        ..clear()
        ..add(Polyline(
          polylineId: const PolylineId('route'),
          points: _route,
          color: Colors.blue,
          width: 4,
        ));
    });
  }
  _lastPosition = position;
  _mapController?.animateCamera(
    CameraUpdate.newLatLng(LatLng(position.latitude, position.longitude)),
  );
});
```

- 총 거리 = 좌표 사이 거리를 계속 더한 값
- 카메라도 현재 위치를 따라가게

### 시간 측정 + 정리

```dart
final _stopwatch = Stopwatch();
Timer? _timer;

void start() {
  _stopwatch.start();
  _timer = Timer.periodic(const Duration(seconds: 1), (_) => setState(() {}));
}

@override
void dispose() {
  _sub?.cancel();          // 위치 스트림
  _timer?.cancel();        // 타이머
  super.dispose();
}
```

> 스트림이랑 타이머를 `dispose`에서 안 끊으면 화면을 나가도 계속 돈다.
{: .prompt-warning }

## 4. 카카오 로그인 (kakao_flutter_sdk)

### 설정 체크리스트

1. Kakao Developers에서 앱 생성 → **네이티브 앱 키**
2. 플랫폼에 Android 패키지명 / iOS 번들 ID 등록
3. **키 해시** 등록 (안드로이드에서 제일 많이 막히는 부분)
4. 동의항목에서 **이메일** 설정 (안 하면 이메일을 못 받음)

```bash
# 디버그 키 해시
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore \
  -storepass android -keypass android | openssl sha1 -binary | openssl base64
```

```xml
<!-- AndroidManifest.xml : 로그인 후 앱으로 돌아오기 -->
<activity
    android:name="com.kakao.sdk.flutter.AuthCodeCustomTabsActivity"
    android:exported="true">
    <intent-filter android:label="flutter_web_auth">
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="kakao네이티브앱키" android:host="oauth"/>
    </intent-filter>
</activity>
```

### 로그인 코드

카카오톡이 설치돼 있으면 앱으로, 없으면 웹(카카오계정)으로.

```dart
KakaoSdk.init(nativeAppKey: '네이티브 앱 키');   // main()

Future<User?> loginWithKakao() async {
  try {
    if (await isKakaoTalkInstalled()) {
      try {
        await UserApi.instance.loginWithKakaoTalk();
      } catch (_) {
        await UserApi.instance.loginWithKakaoAccount();
      }
    } else {
      await UserApi.instance.loginWithKakaoAccount();
    }
    return await UserApi.instance.me();
  } catch (e) {
    return null;
  }
}
```

### 카카오 사용자와 앱 회원 묶기

카카오로 로그인한 사람이 앱 회원 정보와 연결되지 않아서 **한 사람이 두 사람처럼** 저장되는 문제가 있었다.
**이메일을 고유 키**로 기록, 친구, 대결 데이터를 전부 묶고, 이메일 동의가 없으면 저장하지 않고 안내했다.

## 5. Firestore에서 자주 쓴 패턴

### 조건 조회 (배열 포함 + 상태)

```dart
final snap = await FirebaseFirestore.instance
    .collection('battles')
    .where('participants', arrayContains: email)
    .where('status', isEqualTo: 'ongoing')
    .get();
```

> `where` 여러 개 쓰면 **복합 색인** 에러가 난다. 에러 메시지의 링크를 누르면 콘솔에서 바로 만들어준다.
{: .prompt-tip }

### 실시간 화면 갱신 (StreamBuilder)

상대가 뛰면 대결 현황 숫자가 바로 바뀌게.

```dart
StreamBuilder<DocumentSnapshot>(
  stream: FirebaseFirestore.instance.collection('battles').doc(id).snapshots(),
  builder: (context, snapshot) {
    if (!snapshot.hasData) return const CircularProgressIndicator();
    final data = snapshot.data!.data() as Map<String, dynamic>;
    return Text('${data['userProgress']} km');
  },
);
```

### 문서 합치기 저장

```dart
await FirebaseFirestore.instance.collection('users').doc(uid).set({
  'name': name,
  'profileImageUrl': url,
}, SetOptions(merge: true));      // 있는 필드는 유지하고 합치기
```

### 숫자 누적은 increment

```dart
await ref.update({'userProgress': FieldValue.increment(km)});
```

값을 읽어서 더한 다음 다시 쓰면 동시에 업데이트될 때 값이 꼬인다. `increment`는 서버에서 더해줘서 안전하다.

## 참고
- [google_maps_flutter](https://pub.dev/packages/google_maps_flutter)
- [geolocator](https://pub.dev/packages/geolocator)
- [Kakao Developers - Flutter SDK](https://developers.kakao.com/docs/latest/ko/kakaologin/flutter)
- [FlutterFire 공식 문서](https://firebase.flutter.dev/)