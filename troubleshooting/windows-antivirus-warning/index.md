---
title: Windows 안티바이러스 경고
description:
icon:
weight:
author: ["gamb1t","xenix4845"]
번역: ["xenix4845"]
---

[공식 소스](/get-kali/)에서 Kali Linux를 다운로드할 때 Windows 기기에서 안티바이러스 경고가 발생할 수 있어요. 이 경고는 Kali Linux가 Windows가 악성으로 표시하는 많은 침투 테스트(pentesting) 도구를 포함하고 있기 때문에 발생해요. 이는 가끔씩 발생할 수 있는 예상된 동작이에요. 이런 일이 발생하면 다운로드에 대한 [예외 설정](https://support.microsoft.com/ko-kr/windows/windows-%EB%B3%B4%EC%95%88-%EC%95%B1%EC%9D%98-%EB%B0%94%EC%9D%B4%EB%9F%AC%EC%8A%A4-%EB%B0%8F-%EC%9C%84%ED%98%91-%EB%B0%A9%EC%A7%80-1362f4cd-d71a-b52a-0b66-c2820032b65e)을 만들 수 있어요. 다운로드가 완료되면 [체크섬 확인](/introduction/download-official-kali-linux-images/#ISO%EC%9D%98-%EC%84%9C%EB%AA%85-%EC%88%98%EB%8F%99-%ED%99%95%EC%9D%B8%ED%95%98%EA%B8%B0(%EC%A7%81%EC%A0%91-%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C))을 해서 다운로드한 파일이 Kali Linux에서 제공하는 올바른 파일인지 확인하는 것이 좋아요.

이는 윈도우 디펜더(`Windows 보안`이라고도 함) 뿐만 아니라 Bitdefender 및 ESET 등에서도 같은 반응을 보일 수 있어요. 각 백신 프로그램별로 예외를 설정하는 방법은 각 백신의 공식 문서를 읽어주세요. 이곳에 없는 백신이 있을 수도 있어요. 그럴 땐 구글에서 검색하면 찾을 수 있어요.

[Bitdenfer 정상적인 프로그램이 잠재적인 위협으로 차단되어 실행되지 않을 때 (예외처리)](https://www.bitdefenderkorea.co.kr/support/faq.asp?category=6)

[ESET 탐지 제외](https://help.eset.com/eea/11/ko-KR/idh_detection_exclusions.html)

[Kaspersky 예외 설정 - (주)소프트정보서비스](https://softinfo.co.kr/article/%EC%9E%90%EC%A3%BC-%EB%AC%BB%EB%8A%94-%EC%A7%88%EB%AC%B8/3/1844/)

[Avast 및 AVG 단독설치형(개인용/업무용)에서 예외처리 등록](https://blog.avastkorea.com/1424)

[V3 예외 설정하기 - 안랩](https://ask.ahnlab.com/hc/ko/articles/4403366228633-V3%EC%97%90%EC%84%9C-%EC%A7%84%EB%8B%A8%ED%95%98%EB%8A%94-%ED%8C%8C%EC%9D%BC%EC%9D%84-%EA%B2%80%EC%82%AC-%EC%98%88%EC%99%B8-%EB%8C%80%EC%83%81%EC%9C%BC%EB%A1%9C-%EC%84%A4%EC%A0%95%ED%95%98%EA%B3%A0-%EC%8B%B6%EC%8A%B5%EB%8B%88%EB%8B%A4)