---
layout: post
title:  "Windows OS Jekyll Install"
categories: Jekyll
tags: Jekyll windows ruby
---


# Windows OS Jekyll 설치 과정
- Windows 10 Pro / x64 운영체제 환경 기준으로 정리하였다.
- <http://jekyll-windows.juthilo.com/> 문서를 참고하여 작성했다.

***

<br>
- `Jekyll` 은 가장 인기있는 `static site generator` 이다.
- github page에서 블로그를 표현하고 싶다면 지금 포스팅하는 아래와 같이 직접 local 환경을 구성하고 push하여 사용할 수도 있지만,
- 커스텀할 생각이 없다면 굳이 환경을 구성할 필요없이 잘 만들어져있는 테마를 fork / clone 하여 사용해도 좋을 것 같다.
- 또한 Jekyll은 Ruby기반으로 만들어져있는데, Ruby언어에 지식이 없다면 Jekyll이 아닌 다른 static site generator를 사용해도 좋을 것 같다.
	- 나도 Ruby언어에 지식이 없지만, 제일 레퍼런스가 많을 것 같아서 일단 Jekyll로 구축했고 어쩌면 변경할지도 모른다.
	- 다른 static site generator를 알아본다면, 구글에서 static site generator 검색하면 최상단에 뜨는 <https://www.staticgen.com/> 여기서 확인하면 된다.
- 개발자스럽게 블로그를 만들어보고자 시작했는데, 사실 프론트엔드 개발을 꿈꾸진 않아서 굳이 이렇게까지 했어야 했나 싶긴하다.
	- 그래도 뭔가 재밌으니까.
	- 좀 더 공부해서 커스텀을 할 예정이고. <br><br>


***

## Start

### 1. Ruby + Devkit 설치

- <https://rubyinstaller.org/downloads/>
> Ruby, we recommend you use Ruby+Devkit 2.4.X as x64 or x86 installers. 
- 라고 적혀져 있고, 유일하게 볼드 처리되어 있길래 믿음으로 Ruby+Devkit 2.4.4-2 (x64) 를 선택했다.
- 다른 버전을 설치해도 무관하나, Jekyll은 Ruby 2.x 이상 버전에서만 호환된다.

- rubyinstaller-devkit-2.4.4-2-x64.exe 파일을 열어 설치한다. (그냥 Next만 눌렀다)
	![1](https://github.com/Oraindrop/oraindrop.github.io/blob/master/asset/_img/installRuby1.png?raw=true)
	![2](https://github.com/Oraindrop/oraindrop.github.io/blob/master/asset/_img/installRuby2.png?raw=true)
	![3](https://github.com/Oraindrop/oraindrop.github.io/blob/master/asset/_img/installRuby3.png?raw=true)
	![4](https://github.com/Oraindrop/oraindrop.github.io/blob/master/asset/_img/installRuby4.png?raw=true)
	![5](https://github.com/Oraindrop/oraindrop.github.io/blob/master/asset/_img/installRuby5.png?raw=true)	
	
- 커맨드 창을 연다 (Win + R > cmd)
```
gem
```

	위 명령어를 입력했을 때, 아래와 같이 출력되었다면 정상 설치 및 PATH 설정 완료.

	```
	RubyGems is a sophisticated package manager for Ruby.  This is a
	basic help message containing pointers to more information.

	Usage:
	gem -h/--help
	gem -v/--version
	gem command [arguments...] [options...]

	Examples:
	gem install rake
	gem list --local
	gem build package.gemspec
	gem help install

	Further help:
	gem help commands            list all 'gem' commands
	gem help examples            show some examples of usage
	gem help gem_dependencies    gem dependencies file guide
	gem help platforms           gem platforms guide
	gem help <COMMAND>           show help on COMMAND
								   (e.g. 'gem help install')
	gem server                   present a web page at
								 http://localhost:8808/
								 with info about installed gems
	Further information:
	http://guides.rubygems.org
	```

### 2. Jekyll 설치
	
- jekyll 설치 명령어 입력.

	```
	gem install jekyll
	```

	긴긴 뭔가 설치되고 있다는 installing message가 출력되고

	```		
	Done installing documentation for public_suffix, addressable, colorator, http_parser.rb, eventmachine, em-websocket, concurrent-ruby, i18n, rb-fsevent, ffi, rb-inotify, sass-listen, sass, jekyll-sass-converter, ruby_dep, listen, jekyll-watch, kramdown, liquid, mercenary, forwardable-extended, pathutil, rouge, safe_yaml, jekyll after 27 seconds
	25 gems installed
	```

	설치 완료 메세지 확인.
	
- `Syntax highlighter` 는 필요가 없을 듯 하여 Skip. <br><br>

- Jekyll Windows 설치 공식 홈페이지에서 확인한 결과 영어라서 잘은 모르겠지만, Jekyll --watch 라는 기능이 있는데 이게 지킬을 스타트 할때 사용되는 `jekyll -serve` 명령어 기본적으로 포함되어 있는 것 같다.
- 이걸 윈도우에서 사용하려면 뭔가 설치해야 한다. 아래 명령어 입력하고, 

	```
	gem install wdm
	```

	`1 gem installed` 메세지 확인.

- 이제 원하는 경로에 `jekyll new` 명령어를 통해 기본 jekyll 정적 사이트를 생성할 수 있다.

	```
	C:\Users\ChoiSing\Documents>jekyll new myblog
	Could not load Bundler. Bundle install skipped.
	New jekyll site installed in C:/Users/ChoiSing/Documents/myblog.
	```

	생성은 되었는데 `bundler 라는걸 로드할 수 없다`는 것 같다.	
	해당 디렉토리에 기본적인 파일들이 생성된 것을 확인할 수 있고, jekyll 서비스 구동을 시도.

	```
	C:\Users\ChoiSing\Documents>cd myblog
	C:\Users\ChoiSing\Documents\myblog>jekyll serve
	C:/Ruby24-x64/lib/ruby/2.4.0/rubygems/core_ext/kernel_require.rb:55:in `require': cannot load such file -- bundler (LoadError)
	```
	cannot load such file -- bundler, 번들러를 찾을 수 없다고 하니 구글링해서 번들러를 설치하는 명령어를 찾았다.

- 아래와 같이 `gem install bundler` 이후, `bundler install` 명령어를 입력한다.
	
	```
	C:\Users\ChoiSing\Documents\myblog>gem install bundler
	...(중략)
	1 gem installed
	C:\Users\ChoiSing\Documents\myblog>bundler install
	...(중략)
	Bundle complete! 5 Gemfile dependencies, 33 gems now installed.
	Use `bundle info [gemname]` to see where a bundled gem is installed.
	```

	기분 좋은 메시지를 확인한 후 `jekyll serve` 를 재시도 하면 서비스가 구동된다.

	```
	Configuration file: C:/Users/ChoiSing/Documents/myblog/_config.yml
			Source: C:/Users/ChoiSing/Documents/myblog
	   Destination: C:/Users/ChoiSing/Documents/myblog/_site
	Incremental build: disabled. Enable with --incremental
	  Generating...
					done in 0.521 seconds.
	Auto-regeneration: enabled for 'C:/Users/ChoiSing/Documents/myblog'
	Server address: http://127.0.0.1:4000/
	Server running... press ctrl-c to stop.
	```
	
	만약, 추가적인 error가 발생했다면 해당 메시지를 구글에서 검색하면 해결 가능하다.
	
- 이렇게 정상적으로 서비스가 구동되면 `http://localhost:4000/` 에서 `Your awesome blog`를 확인할 수 있다. <br><br>
	![blog](https://github.com/Oraindrop/oraindrop.github.io/blob/master/asset/_img/jekyll_local.png?raw=true)

### 3. Posting	

- 해당 경로의 `_posts` 디렉토리 아래에 markdown 형식으로 파일을 작성하면 된다.
- 파일 제목은 `2018-07-28-welcome-to-jekyll.md` 형식과 유사하게 작성
- 파일 내용에서 아래와 같은 형식을 최상단에 적은 후, 내용을 작성

```
---
layout: post
title:  "awesome Title"
---
```	

- 로컬에 jekyll 설치하여 static site를 생성하는 것 까지 완료했다.
- 좀 더 공부해서 github 연동관련 글을 포스팅 해볼 생각.
	