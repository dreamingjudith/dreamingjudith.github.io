---
layout: post
title: "Raspberry Pi에서 Matplotlib 설치 시 required packages error"
date: 2017-05-04 14:00:53 +0900
categories: [programming, python]
tags: [python, anaconda, matplotlib]
---

얼마 전 라즈베리 파이에 Miniconda를 설치하고 나서 numpy와 matplotlib가 없음을 깨달았다. Anaconda 쓸 땐 몰랐는데, '이럴 줄 알았으면 그냥 맥북에다 설치해서 쓸 걸 그랬나' 하는 생각도 들었다. 그렇지만 모듈이야 설치하면 그만이니까! 그리고 그렇게 나는 또 다시 난관을 만났다.

가벼운 마음으로 `conda install matplotlib`를 터미널에 입력했는데 다음과 같은 출력이 나왔다.

```bash
judith@judith-mate:~$ conda install matplotlib
Fetching package metadata: ....
Error: No packages found in current linux-armv7l channels matching: matplotlib

Did you mean one of these?

    matplotlib-tests

You can search for this package on anaconda.org with

    anaconda search -t conda matplotlib

You may need to install the anaconda-client command line client with

    conda install anaconda-client
```

어지간히 짜증났다. 요새 사람들이 라즈베리 파이로 마인크래프트도 하던데 왜 ARMv7용으로는 matplotlib 패키지도 준비 안 해놨냐! 하지만 안 되면 돌아서라도 가면 된다. Anaconda에서는 조금 구리긴 하더라도 pip로도 모듈을 설치할 수 있다. 그래서 이번에는 `pip install matplotlib` 를 터미널에 입력해봤다.

```bash
judith@judith-mate:~$ pip install matplotlib
Collecting matplotlib
  Downloading matplotlib-2.0.1.tar.gz (53.8MB)
    100% |████████████████████████████████| 53.8MB 863bytes/s
    Complete output from command python setup.py egg_info:
    ============================================================================
    Edit setup.cfg to change the build options

    BUILDING MATPLOTLIB
                matplotlib: yes [2.0.1]
                    python: yes [3.4.3 |Continuum Analytics, Inc.| (default, Aug
                            21 2015, 00:53:08)  [GCC 4.6.3]]
                  platform: yes [linux]

    REQUIRED DEPENDENCIES AND EXTENSIONS
                     numpy: yes [version 1.9.2]
                       six: yes [six was not found.pip will attempt to install
                            it after matplotlib.]
                  dateutil: yes [dateutil was not found. It is required for date
                            axis support. pip/easy_install may attempt to
                            install it after matplotlib.]
               functools32: yes [Not required]
              subprocess32: yes [Not required]
                      pytz: yes [pytz was not found. pip will attempt to install
                            it after matplotlib.]
                    cycler: yes [cycler was not found. pip will attempt to
                            install it after matplotlib.]
                   tornado: yes [tornado was not found. It is required for the
                            WebAgg backend. pip/easy_install may attempt to
                            install it after matplotlib.]
                 pyparsing: yes [pyparsing was not found. It is required for
                            mathtext support. pip/easy_install may attempt to
                            install it after matplotlib.]
                    libagg: yes [pkg-config information for 'libagg' could not
                            be found. Using local copy.]
                  freetype: no  [The C/C++ header for freetype2 (ft2build.h)
                            could not be found.  You may need to install the
                            development package.]
                       png: no  [pkg-config information for 'libpng' could not
                            be found.]
                     qhull: yes [pkg-config information for 'qhull' could not be
                            found. Using local copy.]

    OPTIONAL SUBPACKAGES
               sample_data: yes [installing]
                  toolkits: yes [installing]
                     tests: no  [skipping due to configuration]
            toolkits_tests: no  [skipping due to configuration]

    OPTIONAL BACKEND EXTENSIONS
                    macosx: no  [Mac OS-X only]
                    qt5agg: no  [PyQt5 not found]
                    qt4agg: no  [PySide not found; PyQt4 not found]
                   gtk3agg: no  [Requires pygobject to be installed.]
                 gtk3cairo: no  [Requires cairocffi or pycairo to be installed.]
                    gtkagg: no  [Requires pygtk]
                     tkagg: yes [installing; run-time loading from Python Tcl /
                            Tk]
                     wxagg: no  [requires wxPython]
                       gtk: no  [Requires pygtk]
                       agg: yes [installing]
                     cairo: no  [cairocffi or pycairo not found]
                 windowing: no  [Microsoft Windows only]

    OPTIONAL LATEX DEPENDENCIES
                    dvipng: no
               ghostscript: yes [version 9.18]
                     latex: no
                   pdftops: yes [version 0.41.0]

    OPTIONAL PACKAGE DATA
                      dlls: no  [skipping due to configuration]

    ============================================================================
                            * The following required packages can not be built:
                            * freetype, png

    ----------------------------------------
Command "python setup.py egg_info" failed with error code 1 in /tmp/pip-build-mqe01zwv/matplotlib
```

그러니까 안 됐다는 거잖아 ^^...... 필수 설치요소인 freetype, png 패키지가 제대로 설치되지 않았다는 뜻인 것 같다. 그래서 바로 구글에 검색해봤다. 그리고 역시 모든 개발자들의 문제를 해결해주시는 스택오버플로 형님에서 [이런 답변](http://stackoverflow.com/a/20533455){:target='_blank_'}을 찾았다.

> No. pip will not install system-level dependencies. This means pip will not install RPM(s) (Redhat based systems) or DEB(s) (Debian based systems).
>
> To install system dependencies you will need to use one of the following methods depending on your system.
>
> __Ubuntu/Debian:__
>
> ```bash
> apt-get install libfreetype6-dev
> ```

그래서 나도 재빠르게 터미널에 `sudo apt-get install libfreetype6-dev` 를 입력해봤다.

```bash
judith@judith-mate:~$ sudo apt-get install libfreetype6-dev
[sudo] password for judith:
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following additional packages will be installed:
  libfreetype6 libpng12-dev zlib1g-dev
The following NEW packages will be installed:
  libfreetype6-dev libpng12-dev zlib1g-dev
The following packages will be upgraded:
  libfreetype6
1 upgraded, 3 newly installed, 0 to remove and 160 not upgraded.
Need to get 1,513 kB of archives.
After this operation, 4,725 kB of additional disk space will be used.
Do you want to continue? [Y/n]
```

뭔가 같이 설치되는 패키지 중에 libpng12-dev 라는 게 있는 걸 봐서 이게 한 큐에 모든 것을 해결해줄 것 같아서 일단 설치를 진행했다. 그리고 나서 다시 `pip install matplotlib` 에 도전해봤더니 매끄럽게 설치가 완료됐다. ~~그냥 인터프리터에서 돌리려면 Qt와 같은 백엔드를 또 만져줘야 해서~~ Jupyter에서 돌려봤더니 그래프도 잘 나온다.

![Sin Graph](/media/images/2017-05-04-01.png)*밑바닥부터 시작하는 딥러닝 책에서 예제로 나온 사인 그래프 그리기*

이제 책으로 공부만 하면 될 것 같다. ~~이러고 여태까지 한 글자도 안 읽어봤다.~~
