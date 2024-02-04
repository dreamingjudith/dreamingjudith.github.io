---
layout: post
title: "Windows Terminal에 Miniconda 프로필 추가하기"
categories: [programming, python]
tags: [python, anaconda]
---

작년에 사내 양성과정의 일환으로 진행했던 미니 프로젝트를 마무리하기 위해 오랜만에 윈도우 노트북에 Miniconda를 깔았다. 설치 중간에 따로 `Add PATH` 옵션은 지정하지 않았는데, 막상 Anaconda Prompt를 사용하려니 너무 못 생겨서 대신 Windows Terminal을 사용하기 위해 프로필을 추가하기로 했다. 어차피 Anaconda Prompt도 cmd.exe에 activate 스크립트를 실행하는 추가 인자 정도 붙은 단축어 개념이기 때문에 이 명령줄 인자를 그대로 복사하면 되겠지.

시작 메뉴에서 Anaconda Prompt의 파일 위치를 찾아 연다. (우클릭 드롭다운 메뉴에서 `자세히 > 파일 위치 열기`)

![Windows Explorer](/media/images/2021-09-21-02.png)

이 중에서 나는 Powershell Prompt를 사용할 거니까 `Anaconda Powershell Prompt (miniconda3)` 의 속성을 열어서 **대상**을 확인한다.

![Anaconda Powershell Prompt의 속성](/media/images/2021-09-21-03.png)

전체 명령어는 이렇게 되어 있다.

```
%windir%\System32\WindowsPowerShell\v1.0\powershell.exe -ExecutionPolicy ByPass -NoExit -Command "& 'C:\Users\Judith\miniconda3\shell\condabin\conda-hook.ps1' ; conda activate 'C:\Users\Judith\miniconda3' "
```

여기서 내가 필요한 건 `powershell.exe` 이후의 것들이다. 이제 Windows Terminal을 열어 설정으로 들어가 새로운 프로필을 생성한다. 나는 기존에 있던 Powershell의 프로필을 복제했다.

`Ctrl+,` 단축키로 설정을 열면 제일 밑에 **새 프로필 추가**가 있다.

![Settings](/media/images/2021-09-21-04.png)

**프로필 복제**에서 Windows PowerShell을 선택하고 **중복**을 누른다.

![Profiles](/media/images/2021-09-21-05.png)

그러면 Windows PowerShell (복사) 라는 이름의 프로필이 만들어진다.

![Configuring profiles](/media/images/2021-09-21-06.png)

이제 이름을 바꾸고, **명령줄**에는 아까 복사해둔 전체 명령어에서 `powershell.exe` 이후 부분인 `-ExecutionPolicy`부터 끝까지 복사해 붙여넣어 준다.

![Configuring profiles 2](/media/images/2021-09-21-07.png)

변경사항을 저장한 다음 `PS conda`라는 프로필을 열어 conda를 실행했더니 정상적으로 동작한다.

![conda init](/media/images/2021-09-21-08.png)

이런 방법으로 Anaconda Prompt 대신 Windows Terminal에서 conda를 사용할 수 있게 만들 수 있다.