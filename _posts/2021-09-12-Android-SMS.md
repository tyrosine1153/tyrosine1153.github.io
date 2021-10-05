---
title: "[Unity] Unity에서 안드로이드앱으로 문자 보내기"
excerpt: "Unity에서 Android 앱을 만들때, 문자를 보내는 기능을(???) 구현하기 위해서 사용할 내용입니다. 정말정말 쓸데 없지만 그래서인지 정리된 글이 드물어서 제가 정리했습니다."

categories:
  - Development
tags: 
  - [Unity, Development]

toc: true
toc_sticky: true

date: 2021-09-12
last_modified_at: 2021-10-05

---



## 서론

이번 해 초, 유니티로 문자를 보내는 안드로이드 앱을 만들 일이 있었다. 근데 관련 자료가 그렇게 많지 않아서 많이 헤맸다. 나는 개발할때 헤매면서 공부할 때는 자세히 정리해두는 편이다. 그래서 이것도 마크다운으로 정리했던게 있었고 최근에 찾아서 블로그용으로 정리해서 올리려고 한다. 



Unity에서  안드로이드 앱에서 문자 보내는 예제 코드와 자료에 대해 모은 글이다. 

개발할때 배워서 정리했던 C# 문법도 있는데 개인적으로 보려고 썼던 글이라 남겨두려고 한다.



{% capture notice-2 %}

📚**이 글을 쓰기 위해서 참고한 자료는 글에서 적어 두었다. 이 글의 내용에 대해 더 자세히 알고싶다면 아래의 문서들을 읽어보는 것도 추천한다.**  


📚**필요한 사전 지식**

- Unity Engine에 대한 간단한 사용방법

{% endcapture %}

<div class="notice--info">{{ notice-2 | markdownify }}</div>



## 문자 보내기

### 권한 승인

{% capture notice-2 %}

📚**참고**

- [Unity 공식문서 - Android Manifest](https://docs.unity3d.com/kr/2021.1/Manual/android-manifest.html)
- [Naver 블로그 - Android Manifest 생성 경로 및 커스텀](https://m.blog.naver.com/zldejs22/221763899164) 
- [Tistory 블로그 - 안드로이드 SMS 전송하기](https://travel-nomad.tistory.com/24)

{% endcapture %}

<div class="notice--info">{{ notice-2 | markdownify }}</div>



유니티 앱에서 문자를 보내게 하려면 안드로이드에서 문자 보내기 권한을 승인하도록 해야 한다.

프로젝트 빌드 후 `UnityProject\Temp\StagingArea\AndroidManifest.xml` 또는 `UnityManifest.xml` 을 수정해 `UnityProject\Assets\Plugins\Android\AndroidManifest.xml` 위치에 추가하면 안드로이드 매니페스트를 수정할 수 있다. (매니페스트는 안드로이드 앱에 대한 데이터가 포함된 파일이며, 그중에 하드웨어 기능 지원 및 권한 또한 포함되어 있다. 자세한 내용은 위의 유니티 문서 참고)
다음은 `AndroidManifest.xml` 파일에 추가해야 할 내용이다

```xml
<uses-permission android:name="android.permission.SEND_SMS" />
```

문자를 보내기만 한다면 위의 권한만 추가해도 되지만 문자를 받으려면 아래의 권한도 추가해야 한다.

```xml
<uses-permission android:name="android.permission.SEND_SMS" />
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
```

또, 추가한 권한을 앱에서 실행하려면 사용자가 휴대폰의 애플리케이션 설정에서 권한을 승인하거나 앱을 실행됐을 때 사용자에게 승인요청을 해서 승인을 받아내야 한다. 다음은 앱 실행시 승인요청을 하는 코드이다.

```c#
private readonly string[] permissions = {"android.permission.SEND_SMS", "android.permission.READ_PHONE_STATE", "android.permission.READ_PHONE_STATE"};

private void RequestPermission()
{
# if PLATFORM_ANDROID
	for (int i = 0; i < permissions.Length; i++)
    {
        if (!Permission.HasUserAuthorizedPermission(permissions[i]))
    	    Permission.RequestUserPermission(permissions[i]);
	}
# endif
}
```



### 전송 예제

{% capture notice-2 %}

📚**참고**

- [rmdwirizki gist - Sample How to Send SMS in Android using Unity3D and SMS Manager](https://gist.github.com/rmdwirizki/87f9e68c7ef6ef809a777eb25f12c3b2#file-sendsms-cs-L52)

{% endcapture %}

<div class="notice--info">{{ notice-2 | markdownify }}</div>



안드로이드에서 문자를 관리하려면 안드로이드 개발에서 주로 사용하는 Java의 SMSManager클래스를 사용해야 한다. 그러기 위해서는 여러 방법이 있는데(위의 첫번째 링크 참고) 나는 유니티에서 제공하는 `AndroidJavaClass`와  `AndroidJavaObject`클래스를 사용했다.

```c#
public string SendSMS(string PhoneNumber, string message)
{
    string alert;

    try
    {
        AndroidJavaClass SMSManagerClass = new AndroidJavaClass("android.telephony.SmsManager");
        AndroidJavaObject smsManagerObject = SMSManagerClass.CallStatic<AndroidJavaObject>("getDefault");
        smsManagerObject.Call("sendTextMessage", phoneNumber, null, messageText, null, null);
        alert = "Message sent successfully.";
    }
    catch (Exception e)
    {
        alert = "Error has been Occurred. Fail to send message.\n" +
                "Error : " + e.StackTrace + "\n" + e.Message;
    }

    return alert;
}
```



### Java class SMSManager 사용 비교

{% capture notice-2 %}

📚**참고**

- [Naver 블로그 - 안드로이드 네이티브 메서드 호출 방법](https://m.blog.naver.com/hide1202/220240366851)
- [sitehis 게시물 - SMS, LMS, MMS 발송 방법(Java)](https://www.sitehis.com/spb3/sboard3/read.php?db=talk&uid=556)

{% endcapture %}

<div class="notice--info">{{ notice-2 | markdownify }}</div>



- Java

    ```java
    // SMS
    SmsManager sms = SmsManager.getDefault();
    sms.sendTextMessage(phoneNumber, null, message, null, null);
    ```

    ```java
    // LMS
    SmsManager smsManager = SmsManager.getDefault();

    ArrayList partMessage = smsManager.divideMessage(message);

    smsManager.sendMultipartTextMessage(phoneNumber, null, partsMessage, null, null);
    ```

- C#

    ```c#
    // SMS
    AndroidJavaClass smsManagerClass = new AndroidJavaClass("android.telephony.SmsManager");
    AndroidJavaObject smsManagerObject = smsManagerClass.CallStatic<AndroidJavaObject>("getDefault");
    
    smsManagerObject.Call("sendTextMessage", phoneNumber, null, message, null, null);
    ```

    ```c#
    // LMS
    AndroidJavaClass smsManagerClass = new AndroidJavaClass("android.telephony.SmsManager");
    AndroidJavaObject smsManagerObject = smsManagerClass.CallStatic<AndroidJavaObject>("getDefault");
    
    AndroidJavaObject partsMessage = smsManagerObject.Call<AndroidJavaObject>("divideMessage", message);
    
    smsManagerObject.Call("sendMultipartTextMessage", phoneNumber, null, partsMessage, null, null);
    ```



## 로컬 푸시 알림

📚**참고**

{% capture notice-2 %}

- [Unity 공식문서 - Unity Mobile Notifications Package](https://docs.unity3d.com/Packages/com.unity.mobile.notifications@1.0/)

{% endcapture %}

<div class="notice--info">{{ notice-2 | markdownify }}</div>



`Unity.Notifications.Android`네임스페이스를 사용했다.

- 채널 생성

    ```c#
    private AndroidNotificationChannel smsSendingChannel;

    private void InitChannel()
    {
        smsSendingChannel = new AndroidNotificationChannel
        {
            Id = "SMS",
            Name = "SMS Sending",
            Description = "문자 전송 전/후 알람",
            Importance = Importance.High,
        };
        AndroidNotificationCenter.RegisterNotificationChannel(smsSendingChannel);
    }
    ```

- 알림 보내기

    ```c#
    // 테스트용 푸시 함수
    public void PushNotification1()
    {
        var newNotification = new AndroidNotification
        {
            Title = "Test",
            Text = "'푸시 알림' 버튼을 눌렀을 때 실행되는 알림입니다",
            FireTime = DateTime.Now,
        };
        AndroidNotificationCenter.SendNotification(newNotification, "SMS");
    }
    ```

    ```c#
    public void PushNotification2()
    {
        var newNotification = new AndroidNotification
        {
            Title = "Test",
            Text = "미리 지정된 시간에 실행되는 알림입니다. " + DateTime.Now.AddMinutes(3),
            FireTime = DateTime.Now.AddMinutes(3),
        };
        AndroidNotificationCenter.SendNotification(newNotification, "SMS");
    }
    ```



## C# 문법

### LINQ

{% capture notice-2 %}

📚**참고**

- [Microsoft C# Docs - LINQ 쿼리 소개](https://docs.microsoft.com/ko-kr/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries)

{% endcapture %}

<div class="notice--info">{{ notice-2 | markdownify }}</div>



**LINQ 쿼리의 사용 작업 구분**

1. 데이터 가져오기
2. 쿼리 만들기
3. 쿼리 실행

```c#
# if PLATFORM_ANDROID
// 예약 정보 데이터 가져오기
Reservation[] temps = new Reservation[3];

// today < day <= today+5 의 예약 정보 검색 쿼리 만들기
var reservationQuery = 
    from Reservation reservation in temps
    where DateTime.Compare(Convert.ToDateTime(reservation.date), DateTime.Today.AddDays(5)) <= 0
    && DateTime.Compare(Convert.ToDateTime(reservation.date), DateTime.Today) > 0
    select reservation;

// 쿼리 실행
foreach (Reservation r in reservationQuery)
{
	SendMessage(r.customer.phoneNumber, CreateMessage_Weekly(r));
	// r.isSended = true; // foreach 에서의 반복 변수는 변경할 수 없음
}
# endif
```



### `StringBuilder` 클래스 연산 

: 연산할 때 문자열 객체를 생성해 할당과 해제를 발생시키는 `"str"+"str"`연산에 비해 `StringBuilder` 클래스에서 문자열 연산을 하면 새 객체의 생성 없이 기존의 데이터에 더하는 방식이기 때문에 상대적으로 속도가 빠르며 부하가 적다.

```c#
new StringBuilder(headString).Append(value).ToString()
```



부족한 글이지만 계속해서 수정을 해 나갈 예정입니다. 개선할 부분이 있다면 말씀해주세요. 읽어주셔서 감사합니다.
{: .notice--info}





