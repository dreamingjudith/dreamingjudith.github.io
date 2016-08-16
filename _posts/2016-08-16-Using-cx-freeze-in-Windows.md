---
layout: post
title: "Using cx_freeze in Windows"
date: 2016-08-16 23:01:44 +0900
categories: python cxfreeze
---

회사에서 업무 때문에 Python(이하 파이썬)으로 작성한 스크립트를 바이너리 파일 형태로 컴파일할 필요가 있었다. 그래서 이것저것 시도를 해보다 cx_freeze라는 모듈을 알게 됐다. 회사 컴퓨터는 윈도우다보니 당연히 명령 프롬프트에서 `pip install cx_freeze` 를 입력해 해당 모듈을 설치한 다음 `cxfreeze`라는 명령어로 실행을 시키는데

```
'cxfreeze'은(는) 내부 또는 외부 명령, 실행할 수 있는 프로그램, 또는 배치 파일이 아닙니다.
```

라는 오류와 함께 전혀 돌아가지 않는 것이었다. 분명 설치 잘 했는데! 다른 건 설치하면 바로 돌아가던데! 그래서 `C:\Python27\Scripts` 폴더[^1]에 가봤더니 .exe 파일은커녕 하다 못해 .bat 파일도 없었다.

그렇지만 역시 스택오버플로 형님은 모든 것을 알고 계셨다! 인터넷에 검색해보니 별 도움이 안 되는 답변들 뿐이었는데 [이 답변](http://stackoverflow.com/a/25936813){:target='_blank_'}은 내게 딱 맞는 답을 주었다.

`C:\Python27\Scripts` 폴더, 혹은 본인의 파이썬 설치 경로에 맞게 Scripts 폴더에 가면 다음의 세 개의 확장자 없는 파일들을 볼 수 있다.

- cxfreeze
- cxfreeze-postinstall
- cxfreeze-quickstart

그러면 이 셋 중 cxfreeze-postinstall을 가지고 cx_freeze를 설치한 직후 필요한 작업을 마무리해줘야 한다. 방법은 간단하다. 세 개 파일 모두 파이썬 스크립트라 파이썬으로 돌리기만 하면 된다.

```
python cxfreeze-postinstall
```

해주면 갑자기 배치 파일이 두 개나 생긴다.

- cxfreeze.bat
- cxfreeze-quickstart.bat

이걸로 진짜 cx_freeze의 설치가 끝났다. 이 이후로는 별 문제 없이 cx_freeze를 사용할 수 있었다. (그러나 결국 하려던 작업은 PyInstaller를 통해 했다.)

---
[^1]: 사용하고 있는 파이썬 버전이 2.7.12이다. 따라서 3.X 버전을 사용하고 있거나 다른 드라이브에 설치했다면 경로가 다를 수 있다.
