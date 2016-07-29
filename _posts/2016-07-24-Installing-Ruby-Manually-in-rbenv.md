---
layout: post
title: "Installing Ruby Manually in rbenv"
date: 2016-07-24 15:26:17 +0900
categories: setup ruby rbenv
---

회사에서 rbenv를 쓰려고 했더니만 애최 rbenv 자체가 GitHub 소스를 끌어와서 설치하는 방식이다보니 `git clone`부터 막혔다. 그래도 앞선 글에서 서술했던 것처럼 일단 해결을 했는데 그 다음 rbenv install을 하는 게 문제였다. 여기서도 SSL 검증 때문에 막혔다. 문제는 아무리 회피 방법을 찾아도 안 됐다는 것.

그래서 원하는 Ruby 버전을 수동으로 설치하는 방법을 찾았다. [이 링크](http://passionatedevelopment.com/blog/2012/05/06/install-ruby-manually/){:target="\_blank"}를 참조해 설치를 할 수 있었다. 먼저 [Ruby 사이트](https://www.ruby-lang.org/ko/){:target="\_blank"}에서 소스코드를 다운받아 압축을 풀고, 다음과 같이 빌드했다.

```
./configure --prefix=/home/judith/.rbenv/versions/2.3.1
make
make install
```

참조했던 링크랑 한 가지 다른 점은 configure 할 때 대상 폴더 설정만 해줬다는 것 정도. 그래도 잘 설치가 됐고 `rbenv versions`를 치면 내가 설치해 준 Ruby가 잘 뜨는 것을 확인할 수 있었다.
