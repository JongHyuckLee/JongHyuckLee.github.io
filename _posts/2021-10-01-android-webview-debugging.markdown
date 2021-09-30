---
layout: post
title: "[Webview]Android Webview Debugging"
date: 2021-09-30
tags: android, webview, react, javascript interface, execute function, 리액트, 웹뷰, 자바스크립트
---

# Android Webview Debugging
<pre class="info-panel">
 - Android WebView Debugging에 대해 알아 보겠습니다.
</pre>

## Chrome를 통해 debugging하기
- Android WebView의 경우 Chrome 통해 디버깅을 할 수 있습니다.

- `chrome://inspect` 접속하

<img src="{{site.baseurl}}/images/Android/chromeInspect.png"/>

- `Discover network targets` 설정

<img src="{{site.baseurl}}/images/Android/DiscoverNetworkTargets.png"/>

- WebView IP:PORT 를 입력하기

<img src="{{site.baseurl}}/images/Android/webViewIPAndPort.png"/>

- 아래와 같이 IP:PORT 에 매핑되는 기기가 보입니다.

<img src="{{site.baseurl}}/images/Android/virtualMachine.png"/>

- `inspect` 버튼을 클릭 시 콘솔 창을 볼 수 있습니다.

<img src="{{site.baseurl}}/images/Android/androidConsole.png"/>

