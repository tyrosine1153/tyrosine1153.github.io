---
title: "[Unity] Unity에서 Google Play를 이용해 로그인하기"
excerpt: "Google Play를 연결한 Firebase를 연결한 Unity"

categories:
  - Development
tags: 
  - [Unity, Development]

toc: true
toc_sticky: true

date: 2021-07-10
last_modified_at: 2021-07-10

---



## 서론

새로 시작하는 프로젝트에서 파이어베이스를 사용하기로 했다. 근데 검색을 할 때 많이 어려워서 따로 정리해야 겠다고 생각했다.

파이어베이스를 사용하려는 이유는 인증Authentication 기능을 이용하기 위해서였다. Google Play 게임 서비스를 이용하려고 한다. 빌드 환경은 Android이다.

Firebase를 통해서 Google Play 게임 서비스 로그인을 사용할 때, Google Play 게임에 플레이어를 로그인시키면서 OAuth 2.0 인증 코드를 요청한다. 그리고  `PlayGamesAuthProvider`에 인증 코드를 전달하여 Firebase 사용자 인증 정보를 생성하고 이 인증 정보를 사용해 Firebase에 인증할 수 있다.

다음은 Unity에서 Firebase를 통해 Google Play 게임 서비스 로그인을 하기 전에 필요한 설정에 대해 설명한 내용이다.



{% capture notice-2 %}

📚**이 글을 쓰기 위해서 다음의 자료들을 참고했다. 이 글의 내용에 대해 더 자세히 알고싶다면 아래의 문서들을 읽어보는 것도 추천한다.**  

- [Firebase 공식 문서 - Unity에서 Google Play 게임 서비스를 사용하여 인증](https://firebase.google.com/docs/auth/unity/play-games)


📚**필요한 사전 지식**

- Unity Engine에 대한 간단한 사용방법

{% endcapture %}

<div class="notice--info">{{ notice-2 | markdownify }}</div>





## Unity

### Unity 용 Google Play 게임 플러그인을 설치

[Google Play Github](https://github.com/playgameservices/play-games-plugin-for-unity) 에 들어가 레포지토리에서 Releases 중 제일 최근 버전을 다운받는다. 

파일 중 `current-build`폴더 안에 있는 `GooglePlayGamesPlugin-(version).unitypackage`을 Unity Editor의 Project 창에서 package를 설치할 위치에 끌어와 import한다.



### Firebase Unity SDK 추가

[Firebase 설명서](https://firebase.google.com/docs/unity/setup#set_up_environment) 중 첨부되어있는 Firebase Unity SDK를 다운받는다.

유니티 버전에 따라 dotnet3과 dotnet4 중에 하나를 선택해 그 안에 있는 `FirebaseAuth.unitypackage`을 Google Play 게임 플러그인과 같은 방식으로 프로젝트에 import한다.

* **Unity 5.x 이전 버전** : dotnet3, **Unity 2017.x 이상 버전** : dotnet4 (3도 지원함), **Unity 2019 이상** : dotnet4 (3 지원 중단)



### Package Name 설정

Unity Editor를 켜고 `Build Settings > Player Settings > Other Settings > Identification`에서 Package Name(패키지 이름)을 설정한다. 

- Package Name의 형식 : `com.(companyname).(gamename)`   
  공식문서에서의 [Package Naming 설명](https://docs.unity3d.com/kr/current/Manual/cus-naming.html)



### Unity KeyStore 생성

- 공식 문서에서의 [KeyStore 설명](https://developer.android.com/studio/publish/app-signing?hl=ko#certificates-keystores)

이 프로젝트는 안드로이드 빌드로 예정해 두었기 때문에 안드로이드 앱 배포를 위해서는 KeyStore 파일을 만들어야 한다. Unity Editor에서 KeyStore를 생성하려면 `ProjectSettings - Player(또는 BuildSettings-PlayerSettings) - Android - KetStoreManager `로 간다. 

가지고 있는 KeyStore가 없다면 새로 만들어야 한다. `KeyStore... > Create New > AnyWhere...`로 가서 KeyStore파일을 저장할 경로를 설정하고 KeyStore의 패스워드와 유효기간(년 기준)을 설정한다. 

생성한 KeyStore에 Key를 생성하려면 필수로 Key Alias와 Password를 작성해 Key를 설정한다.

KeyStore가 설정되었다면 **cmd**를 켜고 다음 명령어를 입력해 KeyStore의 모든 정보를 조회할 수 있다. 여기서 **SHA-1 인증서 지문을 얻을 수 있다.**

```bash
keytool -keystore (KeyStore파일 경로) -list -v
```

- 명령어를 입력한 뒤 `'keytool'은(는) 내부 또는 외부 명령, 실행할 수 있는 프로그램, 또는 배치 파일이 아닙니다.`라는 문구만 나올때는 환경 변수에 JDK 경로를 추가한 후 cmd를 재시작하면 된다.

**여기서 설정한 KeyStore은 앞으로 Unity Editor를 열때마다 password를 입력해줘야 안드로이드 빌드가 가능하다.**



### Google Play 게임 플러그인 설정

- **Google Play - 사용자 인증 정보 추가, Android 리소스 스니펫 복사** 후에 해야 함

Unity Editor에서 `Window > Google Play Games > Setup > Android Setup`을 클릭하여 `Android Configuration` 화면을 연다.

`Android Configuration`창의 **Resources Definition**입력칸에 Google Play Console에서 가져온 리소스 스니펫을 붙여넣는다. 

그 밑에 **Web App Client ID**입력칸에 Google Play Console에서 만들었던 OAuth 클라이언트 ID(웹 서버)를 붙여넣는다.



## Firebase

- 공식 문서 - [Firebase 프로젝트 이해](https://firebase.google.com/docs/projects/learn-more)

### Firebase 앱 생성

[Firebase Console](https://console.firebase.google.com/)에 접속해 프로젝트를 추가한다. 추가된 프로젝트에 들어가서 앱을 추가한다. 프로젝트 개요 창 또는 프로젝트 설정에서 추가할 수 있다.

앱의 플랫폼을 고를 때 **Unity**를 선택하고 앱 등록에서 **Android**를 선택한다. Android 패키지 이름에 전에 Unity Editor에서 설정한 Package Name을 붙여넣는다.

구성파일인 `google-services.json`파일을 다운로드 받는다. `google-services.json`은 프로젝트에 고유하게 부여된 **API키, 프로젝트 ID, 어플리케이션 ID** 등 Firebase를 사용할때 클라이언트 데이터와 연결할때 필요한 매개변수(**Firebase 옵션**)를 담고있다. 이 파일을 Asset 아무 곳에 설치한다.

- 공식 문서에서의 [Firebase 구성파일 설명](https://firebase.google.com/docs/projects/learn-more?hl=ko#config-files-objects)





### Google Play 게임을 로그인 제공업체로 사용 설정

- **Google Play - 사용자 인증 정보 추가** 후에 해야 함

Firebase 프로젝트의 좌측 메뉴에서 인증Authentication에 들어간다. 로그인 방법sign-in method 탭에서 로그인 제공업체 중 **Play 게임을 사용 설정**한다. **클라이언트 ID**와 **클라이언트 보안 비밀번호**는 Google Play에서 추가했던 사용자 인증 정보 중 웹 애플리케이션에 포함된 정보이다.

하지만 **클라이언트 보안 비밀번호**는 [Google API 콘솔](https://console.developers.google.com/apis/credentials) 사용자 인증 정보 페이지에서만 얻을 수 있다. 전에 만들었던 프로젝트에 **OAuth 2.0 클라이언트 ID** 섹션에서 **웹 클라이언트(Google 서비스에서 자동 생성)** 세부정보 페이지를 열어 클라이언트 ID와 클라이언트 보안 비밀번호를 확인한다.

Firebase로 돌아와 가져온 클라이언트 ID와 클라이언트 보안 비밀번호를 Play 게임을 로그인 제공업체로 사용 설정 한다.



## Google Play

### Google Play 앱 생성

[Google Play Console](https://play.google.com/console/)에 접속하면 먼저 Google 개발자 계정을 생성해야 한다고 뜬다. 개발자 계정은 25달러를 내고 생성 할 수 있고 이렇게 생성된 계정에서 앱 및 게임들을 관리할 수 있다.

개발자 계정이 생성되면 앱을 만든다. 앱 이름, 기본 언어, 내가 만들려는 프로젝트에 따라 앱과 게임 둘 중 하나를 선택하고, 유료와 무료 중 하나를 선택해 작성한다. 개발자 프로그램 정책과 미국 수출법에 동의하면 앱을 만들 수 있다.

앱을 만들면 대시보드에서 앱을 출시하기 위해 필요한 것을 안내한다. 



### 사용자 인증 정보 추가

- **Unity - Unity KeyStore 생성**후에 해야 함

Google Play Console의 좌측 메뉴 중에서 `성장 > Play 게임 서비스 > 설정 및 관리 > 설정`에 들어가 사용자 인증 정보칸을 찾는다.

- **게임에서 이미 Google API를 사용하고 있나요?** 라는 질문이 떴을 때, 이 프로젝트와 연결된 Google Cloud 나 Firebase의 프로젝트가 있다면 **네,(어쩌구)**를 선택하고 연결된 프로젝트를 선택한다.  
  없다면 **아니요, (어쩌구)**를 선택하고 새로 만든다.

사용자 인증 정보 칸에서 우측 상단에 `사용자 인증 정보 추가`를 눌러 사용자 인증 정보를 추가한다. **Android와 게임 서버 모두 만든다**. 여러 설정을 하고 맨 밑에 인증 칸에서 사용자 인증 정보를 인증할 **OAuth 클라이언트**를 선택한다.

자신이 가지고 있는 OAuth 클라이언트를 선택할 수 있지만 새로 시작하는 경우 **OAuth 클라이언트 ID**를 만들어야 한다. 이때 OAuth클라이언트ID를 만들기 위해 Google Cloud Platform으로 넘어가서 만들 수 있다. **이것도 Android와 게임 서버 모두 만든다.**

- Android OAuth 클라이언트 ID를 생성할 때 SHA-1 인증서 지문을 입력해야 하는데, Unity KeyStore 생성에서 얻었던SHA-1 인증서 지문을 사용하면 된다.



### Android 리소스 스니펫 복사

Google Play Console의 좌측 메뉴 중에서 `성장 > Play 게임 서비스 > 설정 및 관리 > 업적(또는 이벤트, 리더보드)`에 들어가 `업적 만들기`를 선택해 업적을 추가한다.(이벤트와 리더보드 경우에도 각각 업적과 리더보드를 만들면 된다.)

업적은 어떻게 만들더라도 상관없다. 업적을 추가하면 `리소스 보기`를 선택해 Android(XML)탭의 스크립트를 확인한다. XML스크립트의 형식은 다음과 같다. (이벤트 하나를 추가했을 경우)

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--Google Play game services IDs.Save this file as res/values/games-ids.xml in your project.-->
<resources>
  <!-- app_id -->
  <string name="app_id" translatable="false">123456789000</string>
  <!-- package_name -->
  <string name="package_name" translatable="false">com.example.game</string>
  <!-- event Wiped Raid -->
  <string name="event_wiped_raid" translatable="false">CgkIpKjv1a4PEAIYBA</string>
</resources>
```

이 XML스크립트는 Android 리소스 스니펫이다. Google Play게임 서비스 플러그인을 설정하는데 이 스니펫이 필요하다. 스니펫 자체를 얻기 위해 업적이나 이벤트, 리더보드를 추가한 것이기 때문에 추가한 업적 등을 바로 사용하고 싶지 않다면  추가한 업적 등의 부분을 지워주면 된다.  
(위 스크립트에서는  '<!-- event Wiped Raid -->  <string name="event_wiped_raid" translatable="false">...' 부분이 추가한 이벤트가 되겠다. )



부족한 글이지만 계속해서 수정을 해 나갈 예정입니다. 개선할 부분이 있다면 말씀해주세요. 읽어주셔서 감사합니다.
{: .notice--info}
