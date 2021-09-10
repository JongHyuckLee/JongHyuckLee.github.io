---
layout: post
title: "[Webview]IOS Webview 시작하기"
date: 2021-09-10
tags: React-native, webview, react, javascript interface, execute function, 리액트, 웹뷰, 자바스크립트
---

# IOS 웹뷰 시작하기 
<pre class="info-panel">
 - IOS에서 웹뷰 연동 시작하기에 대해 공부해 보겠습니다.
 - 다만 swift 와 ios 개발을 처음 접하다보니 native 개발 지식이 부족할 수 있습니다.
 - webview로 사용될 react 화면은 이전 블로그 글 소스를 이용하겠습니다.
</pre>

## 시작하기
## ios 프로젝트 세팅부터 시작하겠습니다.
- XCode를 엽니다.
- `Create a new Xcode project`를 클릭하여 새 프로젝트 시작.
<img src="{{site.baseurl}}/images/Ios/startIos.png"/>
- Empty Project를 생성합니다.
<img src="{{site.baseurl}}/images/Ios/emptyProject.png"/>
- Story Board로 앱을 생성합니다.
<img src="{{site.baseurl}}/images/Ios/storyBoard.png"/>

## 설정하기
- Info.plist 파일을 엽니다.
- <span class="emphasize">App Transport Security Settings</span>를 추가합니다.
- <span class="emphasize">App Transport Security Settings</span> 하위에 <span class="emphasize">Allow Arbitrary Loads</span>를 추가하고 value를 YES로 바꿔줍니다.
<img src="{{site.baseurl}}/images/Ios/setting.png"/>
### 설치
- <span class="emphasize">App Transport Security Settings</span>
    - ATS(App Transport Security)는 iOS 9에서 도입된 개인정보 보호 기능입니다. 새 앱에서는 기본적으로 이 기능이 사용되며, 보안 연결이 적용됩니다.
    - ATS(App Transport Security)가 안전하지 않은 일반 텍스트 HTTP(http://) 리소스의 로드를 차단했습니다. 앱의 Info.plist 파일을 통해 임시 예외를 구성할 수 있습니다.
    - Apple 측에서 앱의 보안성을 위해 ATS라는 정책을 통해 기본적으로 https 통신을 하도록 강제하고 있습니다.
    - 따라서, 이 정책 변경을 위해 NSAllowsArbitraryLoads 값을 true로 변경해줍니다.
    

## Webview 용 화면 그리기
- 오른쪽 상단의 <span class="emphasize">+</span> 버튼을 클릭합니다.
<img src="{{site.baseurl}}/images/Ios/createStoryBoard.png"/>
- WebKit View 를 클릭해 웹뷰용 화면을 생성합니다.
<img src="{{site.baseurl}}/images/Ios/storyBoardWebView.png"/>
<img src="{{site.baseurl}}/images/Ios/webView.png"/>

## code 작성
### ViewController.swift
{%highlight swift%}
import UIKit
import WebKit
class ViewController: UIViewController, WKUIDelegate {
    
    var webView: WKWebView!
    
    override func loadView() {
        let webConfiguration = WKWebViewConfiguration()
        webView = WKWebView(frame: .zero, configuration: webConfiguration)
        webView.uiDelegate = self
        view = webView
    }
    override func viewDidLoad() {
        super.viewDidLoad()
        
        let myURL = URL(string:"http://localhost:3000/")
        let myRequest = URLRequest(url: myURL!)
        webView.load(myRequest)
}}

{%endhighlight%}
- UIKit : 
    - <span class="emphasize">UIKit</span>은 IOS 앱의 사용자 인터페이스를 구현하고 이벤트를 관리하는 KIT입니다.
    - 사용자 이벤트 처리를 위한 클래스를 포함합니다.
- WebKit:
    - safari 웹 브라우저를 만드는데 기반을 제공하는 오픈 소스 응용 프로그램 프레임워크입다.
- ViewController:
    - 데이터 변화에 뷰들을 업데이트합니다.(React와 비슷한듯 합니다.)
    - 뷰와 사용자간의 인터렉션 제공
    - 화면 사이즈 재조정, 전체적 레이아웃 관리
- UIViewController:
    - 모든 view controller에 공통적인 동작 정의
    - <span class="emphasize">주요 동작</span>
        - 데이터 변화에 따라 컨텐츠 업데이트
        - view와 사용자 인터페이스
        - 전체적인 인터페이스와 레이아웃 관리
- WKUIDelegate:
    - 웹페이지를 대신하여 고유 사용자 인터페이스 요소들을 보여주는 메소드들을 제공.
    - 사용자 인터페이스 관련 작업들을 수행.
    - 이러한 메소드들은 JS 또는 다른 플러그인 컨텐츠를 처리한 결과를 호출 할 수 있음.
- WKNavigationDelegate:
    - 웹 뷰에서 탐색 요청을 수락하고, 불러온 후 완료하는 과정에서 트리거되는 사용자 지정 동작을 구현하는 데 도움이 됨.
- WKWebView:
    - 웹 컨텐츠와 상호 작용을 하는 객체
    - 앱 내에서 웹페이지를 보여줄 수 있는 도구중의 하나.
- WKUserContentController:
    - JS code와 WebView 사이에 인터렉션을 관리할 수 있는 객체입니다.
    - <span class="emphasize">WKUserContentController</span> 객체는 IOS앱과 webview에서 돌고 있는 js code 사이에 브릿지를 제공합니다.
    -  JS 코드를 웹뷰가 돌고있는 웹페이지에 주입합니다.
    - app native code를 통해서 호출할 수 있는 custom js 함수를 설치합니다.
    - 로딩이 막힌 웹페이지를 필터링해줍니다.
- WKWebViewConfiguration:
    - webView를 초기화 할 수 있는 property의 집합입니다.
    - WKWebView 객체를 어떻게 설정할 수 있는지에 대한 정보를 제공합니다.
        - web에서 사용 가능한 쿠키를 초기화합니다.
        - web에서 사용하는 커스텀 url 스킴을 다룹니다.
        - 커스텀 script를 웹페이지에 주입합니다.
        - 컨텐츠를 어떻게 렌더링할지에 대한 커스텀 룰을 결정합니다.
- IOS 생명주기:
    - viewDidLoad -> viewWillAppear -> viewDidAppear -> viewWillDisappear -> viewDidDisappear
      
### App.js
{%highlight react%}
//mobile-webview -> App.js

import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';

ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById('root')
);


{%endhighlight%}
        
## 결과 화면
<img src="{{site.baseurl}}/images/Ios/iosResult.png"/>

<pre class="source">
출처:
- https://developer.apple.com/documentation/webkit/wkwebview
- https://developers.google.com/admob/ios/app-transport-security?hl=ko
- https://kdgt-programmer.tistory.com/71
- https://kathak33.tistory.com/20
- https://caution-dev.github.io/apple-docs/2019/06/25/WKUIDelegate.html
- https://caution-dev.github.io/apple-docs/2019/06/25/WKNavigationDelegate.html
- https://medium.com/a-day-of-a-programmer/wkwebview-%ED%86%BA%EC%95%84%EB%B3%B4%EA%B8%B0-5d830aba11a5

git example : 
- https://github.com/JongHyuckLee/webviewStudy
 
</pre>