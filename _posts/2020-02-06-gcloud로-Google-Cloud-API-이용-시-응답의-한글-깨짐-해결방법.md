---
layout: post
title: "gcloud로 Google Cloud API 이용 시 응답의 한글 깨짐 해결방법"
categories: [programming, python]
tags: [python, google-cloud, gcloud]
---

회사에서 갑자기 [Google Cloud의 Speech-to-text](https://cloud.google.com/speech-to-text) API를 쓸 일이 생겼다. 그래서 부랴부랴 SDK를 깔았다. ([SDK 설치](https://cloud.google.com/sdk/docs/downloads-apt-get){:target='_blank_'}, [SDK 초기화](https://cloud.google.com/sdk/docs/initializing){:target='_blank_'})

API의 응답을 대충 `>`로 받아서 파일로 저장했는데, 한국어 음성을 인식해서 파일로 출력했더니 문제가 발생했다.

```
$ gcloud beta ml speech recognize /home/judith/gcp_speech_test/test.wav --language-code=ko-KR > result
$ cat result
{
  "results": [
    {
      "alternatives": [
        {
          "confidence": 0.9214141,
          "transcript": "?? ??? ?? ?? ?"
        }
      ],
      "languageCode": "ko-kr"
    }
  ]
}
```

내 머리에 물음표가 '?? ??? ?? ?? ?' 뜨기 시작하는 순간. ~~구글 놈들 지들이 분명 한국어 지원한다고 했는데~~ 혹시라도 리턴 자체에 문제가 있는 건가 싶어서 파일로 저장하지 않고 그냥 API 출력을 받았는데 이 때는 출력이 정상이었다.

```
$ gcloud beta ml speech recognize /home/judith/gcp_speech_test/test.wav --language-code=ko-KR
{
  "results": [
    {
      "alternatives": [
        {
          "confidence": 0.9214141,
          "transcript": "강변 자전거 코스 보여 줘"
        }
      ],
      "languageCode": "ko-kr"
    }
  ]
}
```

그래서 이런 저런 시도를 하던 끝에 API의 응답을 Plain text로 리턴받아 Transcript가 유니코드 글자 `u'\uac15\ubcc0 \uc790\uc804\uac70 \ucf54\uc2a4 \ubcf4\uc5ec \uc918'` 형태로 구성되어 있다는 것을 확인할 수 있었다. 그 시점에 `gcloud`가 파이썬 기반의 명령어이며, 내 PC에서는 계속 2.X 버전으로 돌고 있었다는 사실을 깨달았다.

그러면 `gcloud`가 파이썬 3.X로 돌면 되는 것 아닌가? 그런데 PC에는 이미 파이썬이 2.7, 3.5, 3.6 다 깔려 있었고 심지어 나는 아나콘다로 3.6을 쓰고 있었다. 그래서 도대체 뭐 때문에 `gcloud`가 2.7로 도는 것인가 싶어서 구글링을 하다가 `CLOUDSDK_PYTHON` 환경변수가 있다는 사실을 알아냈다. 이걸 `python3`으로 해놓으니 거짓말처럼 한국어 출력이 정상적으로 저장되었다.

```
export CLOUDSDK_PYTHON=python3
```

파이썬 2.X은 한국어 처리 분야의 발전을 위해서라도 빨리 퇴출되어야 한다 (...)
