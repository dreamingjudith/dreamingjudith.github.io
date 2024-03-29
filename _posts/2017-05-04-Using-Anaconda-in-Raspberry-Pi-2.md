---
layout: post
title: "Using Anaconda in Raspberry Pi 2"
date: 2017-05-04 13:47:53 +0900
categories: [programming, python]
tags: [python, anaconda]
---

최근 __밑바닥부터 시작하는 딥러닝__ 이라는 책을 읽기 시작했다. 거의 모든 딥러닝 관련된 뭐시기들이 그렇지만 이 책 역시 파이썬을 기반으로 코드를 작성하고 있어서 개발환경으로 Anaconda(이하 아나콘다) 사용을 권장하고 있다. 그럼 받으면 되기는 한데 ~~분명 개발하려고 산~~ 맥북에다가 이것저것 설치하는 게 너무 싫어서 놀고 있던 라즈베리 파이[^1]를 꺼냈다. 그리고 라즈베리에다가는 Ubuntu Mate 16.04.2 LTS를 설치했다.

이 때까지만 해도 나는 아무 생각이 없었다. 그말인즉, 일반적인 아나콘다 배포판은 라즈베리 파이에 설치할 수 없다는 사실에 대해 인지를 전혀 못 하고 있었다는 뜻. 그치만 방법이 전혀 없는 건 아니었다. 아나콘다보다 훨씬 더 간소화 된 버전인 Miniconda(이하 미니콘다)는 ARM 아키텍처 등도 지원을 해주고 있기 때문이다.

그래서 바로 파이썬 3 버전의 미니콘다를 [다운로드](https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-armv7l.sh){:target='_blank_'}[^2] 받았다. 설치는 일반적으로 아나콘다 설치할 때와 동일하게 진행하면 된다. 근데 이 친구는 numpy도 없고 matplotlib도 없다. 정말 미니멀리즘의 극치이다.

---
[^1]: 라즈베리 파이 2.
[^2]: 안타깝게도 라즈베리 파이 2의 아키텍처인 ARMv7용의 최신 버전이 3.16.0이다.
