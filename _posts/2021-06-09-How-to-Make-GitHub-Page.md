---
title: "[github.io] GitHub Page로 블로그 만들기"
excerpt: "다시 정리해보는 GitHub Page로 블로그 만들고 기초 설정 하는 법"

categories:
  - Blog
tags: 
  - [Blog, jekyll, Github]

toc: true
toc_sticky: true

date: 2021-06-09
last_modified_at: 2021-06-15
---



## 서론

예전에 만들었던 블로그의 기본 설정을 해봤다. 더 해야할 설정이 많은데 그 전에 나중에도 방법을 잊어버리지 않게 블로그 관리하는 법에 대해 정리를 하려고 한다.

이 블로그는 GitHub에서 제공하는 서비스인 **GitHub Pages**와 **Jekyll**을 이용해서 만든 것이다. 

GitHub Pages는 `github.io`라는 도메인을 사용해 GitHub에서 생성한 웹사이트 레포지토리를 게시해주는 **웹호스팅 서비스**이다.  
Jekyll은 블로그 제작을 위해 만들어진 정적 사이트 생성기static site generator이다. 루비를 사용해 만들었으며, GitHub의 공동 설립자 Tom Preston-Werner가 개발했다고 한다.

Jekyll를 이용해 블로그 웹페이지를 만들어서 그걸 GitHub Pages로 호스트 하는 방식이다. 내가 원하는 블로그를 쉽고 간단하게 만들 수 있다.



{% capture notice-2 %}

📚**블로그에서 작성된 용어를 처음 보는 분이 있을 수 있으므로 단어 옆에 그와 관련해 검색하기 좋은 영어 키워드를 적어두었다.** 



📚**이 글을 쓰기 위해서 다음의 자료들을 참고했다. 이 글의 내용에 대해 더 자세히 알고싶다면 아래의 문서들을 읽어보는 것도 추천한다.**  

- [GitHub Page 공식 페이지](https://pages.github.com/)
- [GitHub Page 공식 문서](https://docs.github.com/en/pages)
- [Jekyll 공식 페이지](https://jekyllrb.com/)
- [minimal-mistakes 깃허브 레포지토리](https://github.com/mmistakes/minimal-mistakes)


📚**필요한 사전 지식**

- Git과 GitHub을 다루는 방법 (레포지토리 생성, 클론, 커밋, 푸시)
- Markdown 문법
{% endcapture %}

<div class="notice--info">{{ notice-2 | markdownify }}</div>






## 레포지토리 생성

- [GitHub](https://github.com/)에서 `(username).github.io` 라는 레포지토리를 만든다. (create repository)

  - username : 유저이름이나 오가니제이션 이름

  

  기본적으로 GitHub Pages로 레포지토리를 호스트하기 위해서는 생성한 레포지토리 페이지의 `Setting > Pages > Source`에서 **Branch를 main으로** 설정하면 된다. 그러면`(username).github.io/(repository-name)` 형식의 도메인에 호스트가 된다.
  
  하지만 레포지토리 이름이  `(username).github.io`라면 자동으로 GitHub Page로 `(username).github.io` 형식의 도메인에 호스트가 된다.



- 레포지토리를 로컬에 클론(clone)받는다.



- 클론받은 로컬 저장소에 `index.html`파일을 추가한다.

  - 예시

    ```html
    <!DOCTYPE html>
    <html>
        <body>
            <h> Hello  World</h1>
            <p>I'm hosted with GitHub Pages.</p>
        </body>
    </html>
    ```

  기본적으로 웹 페이지에 접속했을 때, 제일 먼저 보이는 파일은 `index.html`파일이다. index 파일을 추가해 페이지의 처음에 나타날 내용을 간단하게 만들어보자. 테스트 겸 만들어보는 것이고 실제로 쓸 것은 아니다.

  레포지토리를 생성한 다음 아무 파일을 추가하지 않은 채로 바로 웹 페이지에 들어가 보면 404 에러가 뜬다. (`README.md` 파일을  추가했을 경우 readme 파일의 내용이 보여지기도 한다.) 그래서 index 파일을 추가해 정상적으로 페이지가 호스트 되었는지 확인하는 것이다.

  index 파일을 추가했으면 로컬에서 수정된 내용을 커밋(commit)하고 main 브랜치(branch)에 푸시(push)한다. 그러면 웹페이지에 접속했을때 index 파일이 게시되어 있는 것을 볼 수 있다. 일반적으로 웹페이지에는 레포지토리의 main 브랜치의 내용이 나타난다.





## Jekyll 템플릿 클론

깃허브에는 Jekyll로 만든 페이지의 다양한 공식 템플릿 뿐 아니라 다른 사용자들이 올려놓은 템플릿들이 많이 올라와있다. 쉬운 방법으로 블로그를 만들고 싶다면 이 템플릿들을 사용하는 것이 좋다. 

나는 사용자들이 만든 템플릿 중에서 Minimal Mistakes 라는 이름의 템플릿을 이용했다. 앞으로 설명할 내용들은 모두 Minimal Mistakes를 기준으로 작성한 것이다. 사용할 템플릿이 다르다면 Jekyll 페이지끼리의 특성이 비슷한 것을 참고하고 해당 템플릿의 문서를 참고하는 것을 추천한다.



- 템플릿의 레포지토리([Minimal Mistakes Repository](https://github.com/mmistakes/minimal-mistakes))에 들어가 클론(clone)을 하던지 포크(fork)를 하던지 레포지토리의 모든 파일들을 가져온다.

  

- 가져온 템플릿 파일들을 그대로 자신이 만들었던 레포지토리의 로컬 파일에 옮긴다. 템플릿 파일에 옮기면서 전에 만들었던 index파일이 겹친다면 전에 만든건 이제 필요 없으므로 삭제해도 된다.



- 수정된 내용을 커밋, 푸시하고 웹페이지에 접속해 보면 아주 멀끔하고 아무것도 없는 내용을 확인할 수 있다.





## 웹페이지 기본 설정

빈 템플릿을 그대로 가져온 것이기 때문에 막 만든 웹페이지에는 아무것도 없다. 이것을 블로그로 쓰기 위해서는 기본 설정을 해야한다.



- 안쓰는 파일을 삭제한다. 다음 파일들은 레포지토리에서 사용하지 않기 때문에 삭제할 수 있는 파일들이다.
  - `/.github`
  - `/docs`
  - `/test`
  - `.editorconfig`
  - `.gitattributes`
  - `CHANGELOG.md`
  - `minimal-mistakes-jekyll.gemspec`
  - `README.md`
  - `screenshot-layouts.png`
  - `screenshot.png`



- `_config.yml`파일을 수정한다. 레포지토리 폴더 바로 밑에 있으면 된다.  
  이 글에서 한대로라면 config파일은 이미 있기때문에 수정만 하면되지만 없으면 만들어야한다. 다음 [링크](https://github.com/mmistakes/minimal-mistakes/blob/master/_config.yml)에서 가져온 코드를 메모장에 붙여넣고 `_config.yml`라는 이름을 붙이면 된다.  

  - 다음은 config 파일의 매우매우 기본적인 설정들을 설명한 내용들이다.   
    (config 파일에 대한 자세한 내용을 알고싶다면, 나중에 블로그에 다양한 기능을 추가하고 싶다면 관련 공식문서를 읽어보는 것을 추천한다 : [Configuration](https://mmistakes.github.io/minimal-mistakes/docs/configuration/))

    - `minimal_mistakes_skin` : minimal-mistakes의 테마 중 하나를 고를 수 있다. [적용 된 모습](https://mmistakes.github.io/minimal-mistakes/docs/configuration/#site-settings)
    - `title` : 블로그 이름. 웹브라우저 상단 창에 뜨는 이름과 웹페이지 헤더에 나타나는 블로그 명에 표시된다.
    - `name` : 사이트 작성자. 웹페이지의 군데군데에 사이트 작성자 정보를 나타낼 때 사용한다.

    - `description` : 사이트 설명. 검색엔진최적화(SEO)를 개선하기 위해 메타설명에서 사용된다.
    - asdf



- `_data/navigation.yml`을 수정한다.

  navigation파일에서는 웹페이지 헤더에 바로 갈 수 있는 텍스트 링크들을 설정할 수 있다. 초기 화면에서 시작해 이전 단계의 설정까지만 했을 경우 아직 웹페이지 헤더에 **Quick-Start Guide**링크가 있는 것을 볼 수 있다. 

  navigation파일을 열어보면 `title`과 `url` 항목이 묶음으로 있는 것을 볼 수 있을텐데 각각 헤더에 나타나는 텍스트와 그걸 클릭하면 이어지는 링크이다. Quick-Start Guide 항목을 모두 주석처리 하거나 지우고, 자신이 원하는 항목을 추가하자.





## 게시물 작성

레포지토리 폴더 바로 밑에 `_posts`폴더를 생성한다. 이미 있다면 만들지 않아도 된다. 그리고 markdown 확장자 파일로 블로그 게시물을 작성한다. 

- 마크다운은 마크업 언어 중 하나이며, 이를 이용해 일반 텍스트로 서식이 있는 문서를 편하게 작성할 수 있다. 마크다운 전용 문법이 있지만 매우 쉽고 다양한 곳에서 쓰이기 때문에 문법을 숙지해두는 것이 좋다.



블로그 게시글 양식에 따라 마크다운 파일을 작성해야 한다. 원래 이 글에도 적을까 했는데 블로그를 처음 만들었을 때 테스트용으로 게시글을 올리면서 정리한 내용이 있어서 [그 게시물 링크](https://tyrosine1153.github.io/blog/test/#%EB%B8%94%EB%A1%9C%EA%B7%B8-%EC%9E%91%EC%84%B1%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95)를 첨부한다.  





아직 부족한 점이 많아 게시물을 잘 쓰지 못했지만 계속해서 수정을 해 나갈 예정입니다. 틀리거나 개선할 부분이 있다면 말씀해주시면 감사하겠습니다. 읽어주셔서 감사합니다.
{: .notice--info}

