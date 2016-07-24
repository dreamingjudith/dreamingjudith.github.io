---
layout: post
title: "git clone 시 HTTPS 프로토콜로 인한 접속 오류"
date: 2016-07-24 15:18:49 +0900
categories: git config
---

회사에서는 프록시 서버를 쓰는데 간혹 HTTPS 프로토콜로 연결을 시도하는 것들이 안 될 때가 있다. 정확하게 말하자면 될 때보다 안 될 때가 훨씬 더 많다. 이를테면 GitHub에서 소스 코드를 땡겨오기 위해 `git clone`을 쓸 때라던가.

그래서 `git clone`을 쓸 때 다소 위험하지만 SSL 검증 자체를 생략함으로써 프록시 서버의 동작을 회피하는 방법이 있다. 아래의 명령어를 터미널에 입력해서 `.gitconfig`에 SSL 검증 관련 옵션을 추가할 수 있다.

```
git config --global http.sslVerify=false
```

아니면 아래와 같이 환경변수를 추가해줄 수도 있다.

```
export GIT_SSL_NO_VERIFY=1
```

참고로 이 환경변수는 http.sslVerify의 옵션을 오버라이딩 한다. 자세한 내용은 [이 링크](https://git-scm.com/docs/git-config){:target="_blank"}에서 **http.sslVerify** 항목을 찾아보면 된다.
