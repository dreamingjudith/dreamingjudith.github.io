---
layout: post
title: "Installing PyQt4 in Mac OS X"
date: 2016-07-29 22:01:02 +0900
categories: [programming, python]
tags: [python, pyqt]
---

우리집 맥북에 PyQt4를 설치할 일이 생겼다. 이전에 한 번 리눅스 환경에서 설치할 일이 있었는데 맥에서는 처음이었다. 그렇지만 전혀 어렵지 않았다.

먼저 [Riverbank](https://riverbankcomputing.com/news){:target="\_blank"}에 가서 SIP과 PyQt4 소스 코드(tar.gz)를 다운로드 받는다. 화면 우측에 보면 다운로드 링크가 있다.

![Riverbank site](/media/images/2016-07-29-01.png)

여기서 주의할 점은 SIP 같은 경우에는 리눅스와 맥 OS X가 하나의 소스 코드를 사용하지만 PyQt는 그렇지 않다는 점이다. 그러니 _반드시 OS X source인지 확인하고_ 설치하자.

![SIP](/media/images/2016-07-29-02.png)
_SIP은 보다시피 Linux, OS X source가 공통이다._

![PyQt](/media/images/2016-07-29-03.png)
_하지만 PyQt는 그렇지 않다. 맨 밑에 있는 OS X source를 다운받자._

**맥에 Homebrew가 설치되어 있다고 가정하고** (만약 Homebrew가 설치되어 있지 않다면 [이 링크](http://brew.sh){:target="\_blank"}로 들어가서 설치하면 된다.), 먼저 SIP을 설치하자. 다운받은 소스 코드를 `tar -xzvf sip-4.18.1.tar.gz` 와 같은 명령어로 압축해제를 한 다음 풀린 폴더로 이동한다.

그 다음 아래의 명령어들을 차례대로 실행하면 된다.

```
python configure.py
make
make install
```

이걸로 SIP은 설치됐다. 그 다음은 PyQt4 차례다. PyQt의 경우 시스템에 Qt가 먼저 설치되어 있어야 한다. 그래야 configure를 할 때 qmake를 참고해서 설치한다. Homebrew를 이용해 Qt를 설치하는 과정까지 포함해서 아래의 명령어들을 차례대로 실행한다.

```
brew install qt
python configure.py -q /usr/local/bin/qmake
make
make install
```

SIP과 달리 PyQt는 빌드하는데 시간이 오래 걸린다. 그렇지만 위의 절차만 순서대로 따라주면 쉽게 끝낼 수 있다.
