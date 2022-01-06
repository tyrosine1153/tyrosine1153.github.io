---
title: "GitHub Action와 Twitter API를 이용해 트위터 자동 봇 만들기"
excerpt: "한강 물 온도를 알려주는 트위터 자동 봇을 만들었을 때 정리한 내용입니다."

categories:
  - Development
tags: 
  - [Python, Github, Development]

toc: true
toc_sticky: true

date: 2021-10-05
last_modified_at: 2021-10-05

---



## 서론

일정 시간마다 한강 수온을 알리는 트위터 자동봇을 만들기로 했다. 

트위터 자동봇이란, 소셜 네트워크 서비스 트위터([twitter](https://twitter.com/))에 자동으로 게시글을 올리거나 트위터 사용자들을 팔로우 하기 위해 사용되는 프로그램 또는 그것을 이용한 계정을 통틀어 이르는 말이다. 

내가 만들 봇의 기능은 **일정 시간마다 한강 수온 API에서 한강 수온 정보를 가져와서 트위터에 게시하는 것**으로 아주 간단하다.

이를 Twitter API, 한강 수온 API, Python, GitHub의 Action을 이용해 제작하기로 했다.



{% capture notice-2 %}

📚**이 글을 쓰기 위해서 다음의 자료들을 참고했다. 이 글의 내용에 대해 더 자세히 알고싶다면 아래의 문서들을 읽어보는 것도 추천한다.** 

- [GitHub Actions 공식 홈페이지](https://github.com/features/actions)
- [GitHub Actions 공식 문서](https://docs.github.com/en/actions)


📚**필요한 사전 지식**

- Python에 대한 간단한 사용 방법 (사용할 모듈 : os, twitter, **requests**, **json**)
- API에 대한 간단한 이해 (api가 뭔지, 어떻게 생겼는지)
- GitHub에 해단 간단한 이해

📚**준비물**

- [Twitter 계정](https://twitter.com/)
- 한강 수온 API (http://hangang.dkserver.wo.tc, 출처 : [퐁당 - 코드공작꾼](https://play.google.com/store/apps/details?id=com.sangmoo.pongdang))
- [Python Interpreter](https://www.python.org/downloads/)
- [GitHub 계정](https://github.com/)

{% endcapture %}

<div class="notice--info">{{ notice-2 | markdownify }}</div>





## Twitter API 얻기(개발자 신청)

Twitter API를 얻고 사용하기 위해서는 트위터에서 Key와 Token을 발급받아야 한다. 

다음 링크에서 접속해 트위터에 회원가입 후 로그인을 한다.[Twitter Developer](https://apps.twitter.com)

개발자 계정을 신청한다. (나는 가계정을 만들어 신청했을 때 몇시간 동안 검토 후 거절당했는데, 원래 있던 계정으로 다시 신청하니 검토없이 바로 승인되었다.)



## GitHub Actions 사용

실행할 코드가 주기적으로 실행되게 하기 위해서 GitHub Action을 사용한다.

GitHub Action는 GitHub에서 제공하는 CI/CD 도구이다. 
- CI/CD : 테스트, 빌드, 배포 등 작업한 소스코드 빌드와 저장소 적재, 배포까지의 과정을 CI/CD라고 한다.

GitHub Actions에 대한 자세한 설명은 다음 블로그를 참고 : [Github Action에 대한 소개와 사용법](https://velog.io/@ggong/Github-Action%EC%97%90-%EB%8C%80%ED%95%9C-%EC%86%8C%EA%B0%9C%EC%99%80-%EC%82%AC%EC%9A%A9%EB%B2%95)



간단한 코드실행에 쓰기에는 아까운 기능이지만, 잠깐 실행하고 말 생각으로 제작했고 레포지토리 메뉴에 있던게 신기해서 써보고 싶었다.



### Workflow 설정

레포지토리에 GitHub Actions를 생성해보자. 그 전에 GitHub Actions를 생성할 레포지토리도 없다면 새로 만들자. 

레포지토리의 `/.github/workflows` 경로에(경로가 없으면 만들자) workflow를 설정할 yaml파일을 만든다. 나는 `tweet.yml`이라는 이름으로 만들었다.
- 레포지토리 상단 메뉴바의 Actions탭에서 workflow 템플릿을 선택하거나 `set up a workflow yourself`를 선택해 workflow설정 파일을 직접 작성할 수 있다.



만든 workflow 설정 파일을 수정한다. 나는 다음과 같이 작성했다.

```yaml
# .github/workflows/tweet.yml
name: Post tweet

on:
  schedule:
  - cron : "0 23,11 * * *"

jobs:
  run:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout source code 
      uses: actions/checkout@v1

    - name: Set up Python 3.7
      uses: actions/setup-python@v1
      with:
        python-version: 3.7

    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Tweet
      env:
        TWITTER_ACCESS_TOKEN_KEY: ${ { secrets.TWITTER_ACCESS_TOKEN_KEY } }
        TWITTER_ACCESS_TOKEN_SECRET: ${ { secrets.TWITTER_ACCESS_TOKEN_SECRET } }
        TWITTER_CONSUMER_KEY: ${ { secrets.TWITTER_CONSUMER_KEY } }
        TWITTER_CONSUMER_SECRET: ${ { secrets.TWITTER_CONSUMER_SECRET } }
      run: python run.py
```



파일 설정을 한 부분씩 나눠서 살펴보겠다.

```yaml
name: Post tweet

on:
  schedule:
  - cron : "0 23,11 * * *"

jobs:
  run:
    runs-on: ubuntu-latest
```

- `name` : 레포지토리 Actions 탭에 뜨는 이름
- `on` : 워크플로우를 실행하는 특정 활동이나 규칙을 설정.
  - `schedule` : 설정 시간마다 이벤트가 발생되도록 한다. 시간은 cron을 사용해서 설정한다. [cron 문법](https://www.netiq.com/documentation/cloud-manager-2-5/ncm-reference/data/bexyssf.html)에 맞게 시간을 적어주면 된다. (KST기준 매일 8시/20시에 실행되도록, UTC 기준 매일 23시/11시로 설정했다.)
- `jobs` : workflow를 이루는 job들의 목록. `run`이라는 job을 생성.
  - `runs-on` : 워크플로우 실행 환경 설정. 



```yaml
steps:
  - name: Checkout source code 
    uses: actions/checkout@v1

  - name: Set up Python 3.7
    uses: actions/setup-python@v1
    with:
    python-version: 3.7
```

- `steps` : job 안에서 순차적으로 실행되는 task의 단위. 
  - `name` :task의 이름
  - `uses` :어떤 Action을 사용할지. 마켓플레이스에 있는 action을 사용할 수 있다. (`{owner}/{repo}@{ref|version}`의 형태)



```yaml
- name: Install dependencies
    run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
- name: Tweet
	env:
        TWITTER_ACCESS_TOKEN_KEY: ${ { secrets.TWITTER_ACCESS_TOKEN_KEY } }
        TWITTER_ACCESS_TOKEN_SECRET: ${ { secrets.TWITTER_ACCESS_TOKEN_SECRET } }
        TWITTER_CONSUMER_KEY: ${ { secrets.TWITTER_CONSUMER_KEY } }
        TWITTER_CONSUMER_SECRET: ${ { secrets.TWITTER_CONSUMER_SECRET } }
	run: | 
		python run.py
```

- `run` : job마다 할당된 컴퓨팅 자원의 shell로 실행할 커맨드 라인

  - `Install dependencies`에서 프로그램을 실행할 때 필요한 종속성 설치에 대해 작성했다. 파이썬 pip 버전 업데이트, 필요한 파이썬 라이브러리 목록을 레포의 `requirements.txt`파일에 적어서 한번에 설치했다.

- `env` : 해당 job에서 사용할 환경 변수 설정. 'Key/Value'의 형태로 저장한다.

  - 깃허브에서 소스코드를 올릴 때 개인 토큰 키를 그대로 올리면 다른 사람들에게 쉽게 노출된다.  레포지토리 설정에서 암호화된 **변수**를 만들어 개인 키를 숨길 수 있다. 

    1. 레포지토리 상단 메뉴바 -> `Settings` -> `Secrets` (-> `Actions` )

    2. `New repository secret` 클릭

    3. Name (: 변수 이름), Value (: 변수 값) 작성 후 `Add Secret`클릭

       ex) Name : TWITTER_CONSUMER_KEY, Value :123456789asdfQWER

  - 이렇게 만든 깃허브 Secret 변수는 workflow 설정에서 `${ { secrets.[변수 이름] } }`으로 접근할 수 있다.



## 실행 코드 작성 (with Python)

코드를 작성한다.

```python
# run.py
import os
import twitter
import requests
import json

TWITTER_CONSUMER_KEY = os.environ.get("TWITTER_CONSUMER_KEY") or ""
TWITTER_CONSUMER_SECRET = os.environ.get("TWITTER_CONSUMER_SECRET") or ""
TWITTER_ACCESS_TOKEN_KEY = os.environ.get("TWITTER_ACCESS_TOKEN_KEY") or ""
TWITTER_ACCESS_TOKEN_SECRET = os.environ.get("TWITTER_ACCESS_TOKEN_SECRET") or ""

api = twitter.Api(
	consumer_key=TWITTER_CONSUMER_KEY,
	consumer_secret=TWITTER_CONSUMER_SECRET,
	access_token_key=TWITTER_ACCESS_TOKEN_KEY,
	access_token_secret=TWITTER_ACCESS_TOKEN_SECRET,
)

url = 'http://hangang.dkserver.wo.tc/'
result = requests.get(url).text
data = json.loads(result)

str = f"현재 온도는 {data['temp']}도 입니다."
api.PostUpdate(status=str)
```

- 이전에 workflow에서 설정했던 환경 변수에서 트위터의 개인 토큰 키를 가져온다.
- 파이썬의 twitter 라이브러리로 설정한다.
- 미리 준비했던 api를 받아와서 적당한 문자열로 가공한 다음, 설정했던 twitter api로 게시글을 올린다.





부족한 글이지만 계속해서 수정을 해 나갈 예정입니다. 개선할 부분이 있다면 말씀해주세요. 읽어주셔서 감사합니다.
{: .notice--info}
