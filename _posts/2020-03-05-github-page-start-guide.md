---
title: "minimal mistakes 테마로 깃허브 블로그 시작하기"
categories: 
  - blog
read_time: false
last_modified_at: 2020-03-05T16:36:00+09:00
toc: true
---
## Intro

공부하는 내용을 워드로 대충 정리하던 중 체계적인 관리가 필요하다고 느껴 블로그를 만들기로 했다. 티스토리와 깃허브 블로그, velog 사이에서 고민하던 중 그래도 사용자가 많은 이유가 있겠지라는 생각에 깃허브 페이지를 선택했다. 구글링 결과 블로그를 만드는 방법을 알려주는 글은 많았지만 내 지능의 문제인지 블로그 시작부터 쉽지 않았다. 블로그를 시작하는 포스트를 작성하며 점점 블로그를 발전시킬 예정이다.

## 개발 환경 구축

깃허브 블로그를 시작하려면 먼저 루비와 지킬이 설치되어 있어야한다. 블로그를 시작하며 느낀건 딱히 루비와 지킬을 몰라도 상관없다는 것이었다.

### 깃허브 리포지토리 생성

먼저 깃허브로 들어가 리포지토리명을 **[자신의 깃허브 아이디].github.io** 로 하여 리드미 파일과 같이 리포지토리를 생성한다. 그리고 블로그 관리를 원하는 폴더에 clone 한다. 이후 리포지토리 세팅으로 들어가 **Github Pages**로 이동한다.

![깃허브 리포지토리 세팅](/assets/images/리포지토리-설정.png)

마스터 브랜치로 적용하고 다음과 같이 나타나면 된다.

![깃허브 페이지 세팅](/assets/images/페이지-설정.png)

### 루비 설치

<https://rubyinstaller.org/downloads/> 

위의 링크로 들어가 루비를 설치한다. 나는 Ruby devkit 2.6.5-1 (X64)를 선택했다.

![루비 다운로드 페이지](/assets/images/루비-다운로드-페이지.png)

설치후 이런 창이 뜬다면 간단하게 무시하면 된다.

![루비 설치 완료](/assets/images/설치-후.png)

### 지킬 테마 선택

[Minimal Mistakes](https://jekyllthemes.io/theme/minimal-mistakes) 로 이동하여 테마를 가져오자. 다운받은 zip 파일을 원하는곳에 압축을 풀자. 내부에 들어있는 파일들을 이전에 클론한 [깃허브 아이디].github.io 폴더에 모두 옮긴다. 이후 필요없는 몇 가지 파일을 삭제할 수 있다.

- `.editorconfig`
- `.gitattributes`
- `.github`
- `/docs`
- `/test`
- `CHANGELOG.md`
- `minimal-mistakes-jekyll.gemspec`
- `README.md`
- `screenshot-layouts.png`
- `screenshot.png`

### Gemfile 수정

최상위 디렉토리의 Gemfile을 열어 다음과 같이 수정해준다.

```ruby
source "https://rubygems.org"

gem "github-pages", group: :jekyll_plugins

gem "tzinfo-data"
gem "wdm", "~> 0.1.0" if Gem.win_platform?

# If you have any plugins, put them here!
group :jekyll_plugins do
  gem "jekyll-paginate"
  gem "jekyll-sitemap"
  gem "jekyll-gist"
  gem "jekyll-feed"
  gem "jemoji"
  gem "jekyll-include-cache"
  gem "jekyll-algolia"
end
```

### 의존성 설치

윈도우에서 ruby를 검색하고 다음 프로그램을 실행한다.

![루비 커맨드](/assets/images/루비-커맨드.png)

커맨드 창을 열고 테마를 다운받은 폴더로 이동한 후 다음과 같이 입력한다.

- `gem install bundler jekyll`
- `bundle install`
- `gem install bundler`
- `bundle exec jekyll serve`

여기까지 문제 없이 했다면 크롬으로 들어가 <localhost:4000> 으로 접속한다. 

![웹페이지](/assets/images/초기-웹페이지.png)

웹사이트가 만들어 졌다.

## github.io

리포지토리에 변경된 내용들을 깃허브에 push한다면 그 이후로는 [깃허브 아이디].github.io 주소로 자신의 페이지에 접속할 수 있다. 변경 후 바로 적용되지 않으니 쿠키와 캐시등을 삭제하고 접속해 보자. 블로그의 변경을 확인할때는 아까처럼 루비 커맨드를 띄운 후 **jekyll serve** 명령어를 사용한다면 변경된 내용을 이전처럼 로컬 호스트 주소로 확인할 수 있다. 원하는 결과가 나왔다면 깃허브에 **push**해서 저장하도록 하자.

## 참고링크

- [minimal-mistakes 테마로 깃허브 블로그 구축하기]([https://imreplay.com/blogging/minimal-mistakes-%ED%85%8C%EB%A7%88%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%B4-githubio-%EB%B8%94%EB%A1%9C%EA%B7%B8-%EA%B5%AC%EC%B6%95%ED%95%98%EA%B8%B0/](https://imreplay.com/blogging/minimal-mistakes-테마를-이용해-githubio-블로그-구축하기/))
- [테마 제작자의 quick start guide](https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/)