---
layout: post
title: "OS X El Capitan에서 gem install 문제"
date: 2016-07-14 19:02:58 +0900
categories: setup ruby gem
---

휴가라서 요즘 **MongoDB in Action**이라는 책을 정독하고 있다. 한 3장까지 읽으니 예제로 Ruby 드라이버를 활용해서 뭘 어쩌고저쩌고 하라길래 본격적으로 맥북에 개발환경을 갖추자고 마음을 먹었다. 책에서 요구하는 건 MongoDB의 Ruby 드라이버 뿐이긴 하지만 이를 설치하기 위해선 RubyGems가 있어야 했다.

_(참고로 OS X El Capitan 기준으로 맥북에는 RubyGems 2.0.14.1이 이미 설치되어 있었으며, 오늘자 기준으로 RubyGems의 최신 버전은 2.6.6이다.)_

그래서 우선 [Homebrew](http://brew.sh){:target="_blank"}[^1]를 설치했다. (물론 굳이 Homebrew를 설치할 필요는 없었는데 왜 설치했는지는 나도 잘 모르겠다. 하지만 이 행위가 나중에는 엄청난 신의 한 수가 됐다.) 그런 다음 [RubyGems.org](https://rubygems.org){:target="_blank"}에서 최신 버전 소스코드를 다운받아 업그레이드 설치를 했다[^2]. 이제 MongoDB 드라이버를 설치할 때가 됐다 싶어 명령어를 실행

	gem install mongo

하니 다음과 같은 에러 메시지가 출력됐다.

	While executing gem ... (Errno::EPERM)
	  Operation not permitted - /usr/bin/coderay

전에도 한 번 El Capitan에서 Homebrew를 설치하려다 비슷한 권한 문제를 경험한 적이 있어 '필시 이것은 El Capitan부터 바뀐 맥의 정책 문제이다' 싶어 재빨리 구글에 검색해봤더니 역시나 그런 문제였다. 그래서 스택오버플로의 [본 답변](http://stackoverflow.com/a/32892222){:target="_blank"}처럼 설치 경로를 `/usr/local/bin`으로 지정해 설치했다. **왜냐하면 나는 이미 Homebrew를 설치했으니까!**

![Installing gem](/media/images/2016-07-14-01.png)
_설치가 잘 되고 있음. 내친 김에 Jekyll도 설치했다_

gem을 어떻게 설치할 것인가에 대해 다른 옵션들도 존재하는 것 같다. [본 링크](http://stackoverflow.com/q/31972968){:target="_blank"}를 참조하면 몇 가지 대안을 얻을 수 있을지도 모른다.

---

# [2016-07-15 추가]

확인해보니 아무래도 gem 패키지가 꼬인 것 같다. 패키지 설치 위치를 /usr/local/bin으로 잡아도 애초에 Ruby 자체가 맥에 원래 깔려있는 거다 보니 `require 'mongo'` 등을 할 때 /usr/local/bin에서 못 찾고 엄한 데로 가는 것 같다. 그래서 그냥

1. [Homebrew 삭제](https://github.com/Homebrew/brew/blob/master/share/doc/homebrew/FAQ.md#how-do-i-uninstall-homebrew){:target="_blank"} 뒤 다시 설치
2. `brew install ruby`
3. `which ruby` 했을 때 `/usr/local/bin/ruby`로 나오는 거 확인
4. RubyGems 홈페이지에서 소스코드 다운받아 다시 설치
5. 설치했던 gem들은 그대로 둔 상태에서 `gem install mongo` 와 같이 그냥 설치

했더니 지금은 이런 모양이다.

![/usr/local/bin](/media/images/2016-07-15-01.png)

![gem list](/media/images/2016-07-15-02.png)

<del>그렇지만 여전히 `require 'rubygems'` 해도 패키지가 없다는 둥의 오류를 낸다. 그래 뭐 어차피 설치하다 막 꼬였다고 맥북 또 포맷하는 것도 귀찮으니 책의 튜토리얼 따라하는 건 포기하고 그냥 이렇게 살련다.</del> 이 문제는 `irb -r connect.rb` 했더니 connect.rb를 못 찾아서 `irb -r ./connect.rb` 했더니 완전 잘 되는 걸로.

---
[^1]: Homebrew는 일종의 맥을 위한 패키지 관리자 같은 거라고 한다. 데비안 계열 리눅스에서 apt-get과 비슷한 거라고나 할까?
[^2]: <del>왜 그런지 모르겠으나 사이트에서 알려주는 `gem update --system` 이라는 명령어가 `ERROR:  Invalid option: --update.  See 'gem --help'.`이라는 에러 메시지만 돌려주기 때문이었다.</del> <br />**[2016-07-14 추가]** 이제 보니 그냥 내가 오타낸 거였다. 어휴 바보.