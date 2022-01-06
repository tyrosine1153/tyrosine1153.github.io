---
title: "[Unity] 유니티 데이터 저장하기"
excerpt: "유니티에서 데이터를 저장하는 여러 방법(PlayerPrefs, CSV, JSON)을 정리했습니다."

categories:
  - Unity
tags: 
  - [Unity]

toc: true
toc_sticky: true

date: 2021-12-31
last_modified_at: 2021-12-31
---



## 서론

오랜만에 새로운 글을 올리고 싶었는데, 이번에도 예전에 작성했던 글을 정리해서 올리게 되었다. 블로그에 올릴 글을 작성하다보면 사소한 부분에서 신경을 많이 쓰게 되는데, 그래서 시간이 많이 걸리고 글도 잘 안 쓰게 되는 것 같다. 최근에야 로컬 서버를 열어서 수정한 블로그를 미리 확인 할 수 있다는 것을 알아서 그걸로 좀 더 작성해 보려고 한다.

다음은 Unity에서 데이터를 저장 할 때 사용한 방법에 대해서 정리한 내용이다. PlayerPref, CSV, JSON을 이용한 방법을 각각 조사했는데 방법마다 저장하기 용이한 데이터, 장단점이 다르니 그런 부분을 참고하는게 좋을 듯 하다.



{% capture notice-2 %}

📚**이 글을 쓰기 위해서 다음의 자료들을 참고했다. 이 글의 내용에 대해 더 자세히 알고싶다면 아래의 문서들을 읽어보는 것도 추천한다.** 

- [PlayerPrefs 클래스 - 유니티 공식문서](https://docs.unity3d.com/ScriptReference/PlayerPrefs.html)
- [CSV(파일 형식) - 위키백과](https://ko.wikipedia.org/wiki/CSV_(%ED%8C%8C%EC%9D%BC_%ED%98%95%EC%8B%9D))
- [CSV - 해시넷](http://wiki.hash.kr/index.php/CSV)
- [유니티에서 JSON 사용하기 - 베르의 프로그래밍 노트](https://wergia.tistory.com/164)
- [JSON - 위키백과](https://ko.wikipedia.org/wiki/JSON)
- [JSON 직렬화 - 유니티 공식문서](https://docs.unity3d.com/kr/2019.4/Manual/JSONSerialization.html)
- [직렬화 - 위키백과](https://ko.wikipedia.org/wiki/%EC%A7%81%EB%A0%AC%ED%99%94)
- [FileStream 생성자 - C# 공식문서](https://docs.microsoft.com/ko-kr/dotnet/api/system.io.filestream.-ctor?view=net-5.0)
- [Unity Application.dataPath(유니티 데이터 저장 경로) - 3DMP](https://3dmpengines.tistory.com/1745)
- [JsonUtility 클래스 - 유니티 공식문서](https://docs.unity3d.com/kr/2019.4/ScriptReference/JsonUtility.html)

📚**필요한 사전 지식**

- C#으로 작성한 Unity Scripts를 읽을 수 있다.

{% endcapture %}

<div class="notice--info">{{ notice-2 | markdownify }}</div>





## PlayerPrefs

유니티의 클래스인 PlayerPrefs의 함수를 이용해 로컬에 데이터를 저장할 수 있다. **int, float, string자료형의 변수**로 저장이 가능하다.  string인 Key와 저장할 자료형인 Value가 짝을 지어 저장된다.

Window Standalone 빌드에서 PlayerPrefs로 저장된 값은 레지스트리의 `HKEY_CURRENT_USER\Software\[company name]\[product name]` 에 저장이 된다. Window Unity Editor에서 저장된 값은 `HKCU\Software\Unity\UnityEditor\[ExampleCompanyName]\[ExampleProductName]`에 저장이 된다.

다음은 내 윈도우의 레지스트리 편집기에서 유니티 에디터에서 저장된 값을 확인한 모습이다.

![211231_editor](https://user-images.githubusercontent.com/63496908/148334601-ab0bce03-aefd-4dd2-9312-1d6092d7f745.png)

그 외에 다른 플랫폼의 저장 경로는 [PlayerPrefs의 공식 문서](https://docs.unity3d.com/ScriptReference/PlayerPrefs.html)에서 참고할 수 있다.

### 장/단점

- 데이터의 저장과 불러오는 것이 매우 쉽다.
- 기본적으로 데이터를 저장할 때 암호화가 되어있지 않기때문에 보안에 취약하다.

### 정적 함수

| 함수명                                      | 설명                                       |
| ---------------------------------------- | ---------------------------------------- |
| [DeleteAll](https://docs.unity3d.com/kr/2020.3/ScriptReference/PlayerPrefs.DeleteAll.html) | Removes all keys and values from the preferences. Use with caution. |
| [DeleteKey](https://docs.unity3d.com/kr/2020.3/ScriptReference/PlayerPrefs.DeleteKey.html) | Removes the given key from the PlayerPrefs. If the key does not exist, DeleteKey has no impact. |
| [GetFloat](https://docs.unity3d.com/kr/2020.3/ScriptReference/PlayerPrefs.GetFloat.html) | Returns the value corresponding to key in the preference file if it exists. |
| [GetInt](https://docs.unity3d.com/kr/2020.3/ScriptReference/PlayerPrefs.GetInt.html) | Returns the value corresponding to key in the preference file if it exists. |
| [GetString](https://docs.unity3d.com/kr/2020.3/ScriptReference/PlayerPrefs.GetString.html) | Returns the value corresponding to key in the preference file if it exists. |
| [HasKey](https://docs.unity3d.com/kr/2020.3/ScriptReference/PlayerPrefs.HasKey.html) | Returns true if the given key exists in PlayerPrefs, otherwise returns false. |
| [Save](https://docs.unity3d.com/kr/2020.3/ScriptReference/PlayerPrefs.Save.html) | Writes all modified preferences to disk. |
| [SetFloat](https://docs.unity3d.com/kr/2020.3/ScriptReference/PlayerPrefs.SetFloat.html) | Sets the float value of the preference identified by the given key. You can use PlayerPrefs.GetFloat to retrieve this value. |
| [SetInt](https://docs.unity3d.com/kr/2020.3/ScriptReference/PlayerPrefs.SetInt.html) | Sets a single integer value for the preference identified by the given key. You can use PlayerPrefs.GetInt to retrieve this value. |
| [SetString](https://docs.unity3d.com/kr/2020.3/ScriptReference/PlayerPrefs.SetString.html) | Sets a single string value for the preference identified by the given key. You can use PlayerPrefs.GetString to retrieve this value. |

### 예제

```csharp
public int level = 3;
```

PlayerPrefs로 저장할 데이터를 만든다. int, float, string 자료형인 변수면 모두 가능하다.

PlayerPrefs의 함수를 이용해 저장하고 불러올 수 있다.

```csharp
PlayerPrefs.SetInt("level", level);
level = PlayerPrefs.GetInt("MyClass_level", 0);
```



## CSV

CSV는 comma-separated values 또는 comma-separated variables의 줄임으로, 데이터를 쉼표(,)와 엔터(\n)로 구분한 텍스트 데이터/파일이다. 확장자 `.csv`로 나타낸다. 표 형태의 데이터 구조를 가지고 있기 때문에 **스프레드시트나 데이터베이스의 저장**에 용이하다. 엔터로 구분한 한 줄이 한 개의 행으로, 열 사이를 쉽표를 넣어 구분한다.

유니티에서 지원하는 클래스는 따로 없기 때문에 직접 파일 입출력을 통해 구현을 한다.

### 장/단점

- Plain Text로 작성되고 파일의 규칙이 간단하기 때문에 편집 기능 구현의 난이도가 낮고, 직접 편집하기도 쉽다. 
- 스프레드시트나 엑셀에서 CSV파일로 가져오기 쉽다.
- 저장할 데이터의 문자열에 쉼표가 있는 경우 다른 열로 해석될 수 있기 때문에 관리에 주의해야 한다.
  - 보통 쉼표가 포함된 문자열의 경우 큰따옴표(")로 감싼다.

### 기본구조

| 연도   | 제조사   | 모델                                     | 설명                               | 가격      |
| ---- | ----- | -------------------------------------- | -------------------------------- | ------- |
| 1997 | Ford  | E350                                   | ac, abs, moon                    | 3000.00 |
| 1999 | Chevy | Venture "Extended Edition"             |                                  | 4900.00 |
| 1999 | Chevy | Venture "Extended Edition, Very Large" |                                  | 5000.00 |
| 1996 | Jeep  | Grand Cherokee                         | MUST SELL!air, moon roof, loaded | 4799.00 |

위의 데이터 표는 다음의 CSV형식으로 표현할 수 있다.

```
연도,제조사,모델,설명,가격
1997,Ford,E350,"ac, abs, moon",3000.00
1999,Chevy,"Venture ""Extended Edition""","",4900.00
1999,Chevy,"Venture ""Extended Edition, Very Large""",,5000.00
1996,Jeep,Grand Cherokee,"MUST SELL!air, moon roof, loaded",4799.00
```

### 예제

보통 Unity에서 CSV를 변환하는 예제를 찾다보면 계속해서 나오는 코드조각이 있다. CSVReader라는 클래스에 대해 작성한건데 주변에서는 이 코드를 쓰는걸 자주봤다. 출처는 [Lightweight CSV reader for Unity](https://bravenewmethod.com/2014/09/13/lightweight-csv-reader-for-unity). 코드가 더러워서 나는 좀 싫다.

```csharp
using UnityEngine;
using System.Collections.Generic;
using System.Text.RegularExpressions;

public class CSVReader
{
    private const string SPLIT_RE = @",(?=(?:[^""]*""[^""]*"")*(?![^""]*""))";
    private const string LINE_SPLIT_RE = @"\r\n|\n\r|\n|\r";
    private static readonly char[] TRIM_CHARS = {'\"'};

    public static List<Dictionary<string, object>> Read(string filePath)
    {
        var list = new List<Dictionary<string, object>>();
        var data = Resources.Load<TextAsset>(filePath);

        var lines = Regex.Split(data.text, LINE_SPLIT_RE);
      
        if (lines.Length <= 1) return list;

        var header = Regex.Split(lines[0], SPLIT_RE);
        for (var i = 1; i < lines.Length; i++)
        {
            var values = Regex.Split(lines[i], SPLIT_RE);
            if (values.Length == 0 || values[0] == "") continue;

            var entry = new Dictionary<string, object>();
            for (var j = 0; j < header.Length && j < values.Length; j++)
            {
                var value = values[j];
                value = value.TrimStart(TRIM_CHARS).TrimEnd(TRIM_CHARS).Replace("\\", "");
                object finalValue = value;
                if (int.TryParse(value, out var n))
                {
                    finalValue = n;
                }
                else if (float.TryParse(value, out var f))
                {
                    finalValue = f;
                }
                entry[header[j]] = finalValue;
            }
            list.Add(entry);
        }
        return list;
    }
}
```

할 수 있는 만큼만 조금 수정해봤다. 이 코드를 사용해서 CSV파일을 해석해보겠다.

아래와 같은 내용을 가진 `example.csv` 파일이 유니티 프로젝트의 Resource폴더 하위에 있을 때,

```
name,age,speed,description
cat,2,4.5,"cat stalks, jumps and meows"
dog,2,5.5,dog barks
fish,1,1.1,fish swims
```

CSVReader로 다음과 같이 데이터를 사용할 수 있다.

```csharp
... private void Awake()
var data = CSVReader.Read("example");
for (var i = 0; i < data.Count; i++)
{
    print($"name : {data[i]["name"]} age : {data[i]["age"]} speed : {data[i]["speed"]} desc : {data[i]["description"]}");
}
```





## JSON

JSON(JavaScript Object Notation)은 웹이나 네트워크에서 **서버와 클라이언트 사이에서 데이터를 주고받을때** 사용하는 개방형 표준 포멧으로, 텍스트를 사용하기 때문에 사람이 이해하기 쉽다는 장점이 있다. JSON은 사용범위가 거의 일치하는 XML에 비해 가독성이 좋고 직렬화와 비직렬화 함수를 통해 데이터와 JSON 데이터를 편하게 변환할 수 있다는 장점을 가지고 있다.

유니티에서는 JsonUtility 클래스를 사용해 오브젝트를 JSON포맷으로 상호 작용할 수 있다. 

**직렬화** : 데이터 구조나 오브젝트 상태를 동일하거나 다른 컴퓨터 환경에 저장하고 나중에 재구성할 수 있는 포맷으로 변환하는 과정

### 장점

- 텍스트로 이루어져 사람과 기계 모두 읽고 쓰기 쉽다.
- 프로그래밍 언어와 플랫폼에 독립적이므로 서로 다른 시스템간에 객체를 교환하기에 좋다.

### 기본 구조

키와 값의 쌍으로 이루어진 데이터를 저장한다. 기본 자료형은 수(정수, 실수), 문자열, boolean, 배열, 객체, null을 지원한다. `{ }`는 객체를 의미하고, `[ ]`는 순서가 있는 배열을 나타낸다. 

```json
{
    "이름": "홍길동",
    "나이": 25,
    "성별": "여",
    "주소": "서울특별시 양천구 목동",
    "특기": ["농구", "도술"],
    "가족관계": {"#": 2, "아버지": "홍판서", "어머니": "춘섬"},
    "회사": "경기 수원시 팔달구 우만동"
}
```


### 정적 함수

| 함수명                                      | 설명                                       |
| ---------------------------------------- | ---------------------------------------- |
| [FromJson](https://docs.unity3d.com/kr/2019.4/ScriptReference/JsonUtility.FromJson.html) | Create an object from its JSON representation. |
| [FromJsonOverwrite](https://docs.unity3d.com/kr/2019.4/ScriptReference/JsonUtility.FromJsonOverwrite.html) | Overwrite data in an object by reading from its JSON representation. |
| [ToJson](https://docs.unity3d.com/kr/2019.4/ScriptReference/JsonUtility.ToJson.html) | Generate a JSON representation of the public fields of an object |

### 예제

JSON 직렬화는 구조화된 JSON을 사용한다.  JSON 데이터에 저장하려는 변수를 설명하는 클래스 또는 구조를 만든다. 변수들이 있는 클래스를 정의하고 `[Serializable]` 속성으로 표시해 JSON 시리얼라이저와 연동되도록 한다.

```c#
[Serializable]
public class MyClass
{
    public int level;
    public float timeElapsed;
    public string playerName;
}
```

그 다음, 클래스의 인스턴스를 만들고

```c#
MyClass myObject = new MyClass();
myObject.level = 18;
myObject.timeElapsed = 1.12f;
myObject.playerName = "World Star Jungmin";
```

` JsonUtility.ToJson` 메서드를 사용해 JSON 포맷으로 전환한다.

JSON을 다시 오브젝트로 전환하려면 `JsonUtility.FromJson` 메서드를 사용한다.

```c#
string json = JsonUtility.ToJson(myObject);
/* json now contains: 
'{
    "level":18,
    "timeElapsed":1.12f,
    "playerName":"World Star Jungmin"
}'*/
myObject = JsonUtility.FromJson<MyClass>(json);  // 대충 json을 MyClass 형식의 인스턴스로 전환한다는 뜻
```

\* 새 인스턴스가 생성되고 JSON 데이터를 사용해 인스턴스의 값이 설정된다.  JSON 데이터에 클래스에 없는 값이 있으면 해당 값은 무시되며, 클래스에 있는 값이 누락되면 구성된 값을 오브젝트에 그대로 유지한다.

기존에 있던 오브젝트에 JSON데이터를 역직렬화해 기존 데이터를 덮어쓸 수 있다.

JSON데이터에 필드에 대한 값이 없으면 시리얼라이저는 해당 필드의 값을 변경하지 않는다. 이를 통해 일부만 작성된 JSON으로 오브젝트를 덮어써 `패치`를 할 수 있다 

```c#
JsonUtility.FromJsonOverwrite(json, myObject);
```

#### 데이터 파일로 저장하고 불러오기

- 파일 저장 예제

```c#
public void SaveByJson<T>(string filePath, string fileName, T obj)
{
    var fileStream = new FileStream($"{filePath}/{fileName}", FileMode.Create);  
    var jsonData = JsonUtility.ToJson(obj);
    var data = Encoding.UTF8.GetBytes(jsonData);
    fileStream.Write(data, 0, data.Length);  // byte로 인코딩 한 것을 파일스트림에 작성
    fileStream.Close();
}

...private void Start()
var obj = new MyClass(true);
SaveByJson<MyClass>(Application.dataPath, "MyClass", obj); 
// 윈도우-프로젝트디렉토리/Assets
```

- 파일 불러오기 예제

```c#
public T LoadByJson<T>(string filePath, string fileName)
{
	var fileStream = new FileStream($"{filePath}/{fileName}", FileMode.Open);
	var data = new byte[fileStream.Length];
    var jsonData = Encoding.UTF8.GetString(data);
	fileStream.Read(data, 0, data.Length);
	fileStream.Close();
  
	return JsonUtility.FromJson<T>(jsonData);
}

...private void Start()
var obj = LoadByJson<MyClass>(Application.dataPath, "MyClass");
```

Json외에도 다른 확장자의 파일은 모두 이와 같은 방식(FileStream)으로 저장할 수 있다.





부족한 글이지만 계속해서 수정을 해 나갈 예정입니다. 개선할 부분이 있다면 말씀해주세요. 읽어주셔서 감사합니다.
{: .notice--info}