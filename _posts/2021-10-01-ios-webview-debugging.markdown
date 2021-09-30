---
layout: post
title: "[Webview]IOS Webview Debugging"
date: 2021-09-30
tags: ios, webview, react, javascript interface, execute function, 리액트, 웹뷰, 자바스크립트
---

# IOS Webview Debugging
<pre class="info-panel">
 - IOS WebView Debugging에 대해 알아 보겠습니다.
</pre>

## Safari를 통해 debugging하기
- IOS WebView의 경우 Safari를 통해 디버깅을 할 수 있습니다.

## 개발자용 탭 열기
- 안드로이드 폰 개발자용 기능 켜기와 같이 사파리 개발자 탭을 열어야 합니다.
- 아무 설정이 없다면 아래와 같이 개발자용 탭이 없습니다.

<img src="{{site.baseurl}}/images/Ios/closedDeveloperTab.png"/>

- IOS 디버깅을 위해서는 사파리에서 개발자 탭을 열어야 합니다.

- Safari > 환경설정 클릭

<img src="{{site.baseurl}}/images/Ios/safariTab.png"/>

- `메뉴 막대에서 개발자용 메뉴 보기` 메뉴 클릭

<img src="{{site.baseurl}}/images/Ios/setDeveloperSetting.png"/>

- `개발자용` 메뉴가 생긴것을 볼 수 있습니다.

<img src="{{site.baseurl}}/images/Ios/openDeveloperTab.png"/>

- Webview 서버가 돌고있는 ip:port 를 선택합니다. (여기서는 localhost입니다.)

<img src="{{site.baseurl}}/images/Ios/webViewLocalhost.png"/>

- debugging console을 확인할 수 있습니다.

<img src="{{site.baseurl}}/images/Ios/safariDebugging.png"/>