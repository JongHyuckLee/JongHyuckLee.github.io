---
layout: post
title: "[Webview]Android Webview Android와 WebView 데이터 교환"
date: 2021-09-30
tags: android, kotlin, webview, react, javascript interface, execute function, 리액트, 웹뷰, 자바스크립트
---


# Android Webview Android와 WebView 데이터 교환하기
<pre class="info-panel">
 - Android와 WebView간 데이터 교환에 대해 알아보겠습니다.
 - 다만 Kotlin 과 android 개발을 처음 접하다보니 native 개발 지식이 부족할 수 있습니다.
 - kotlin - webView 연동 글을 보고 싶다면 앞 선 글을 참고하면 됩니다.
 - https://jonghyucklee.github.io/2021/09/18/start-android-webview/
</pre>

## Android 코드 작성
### MainActivity.kt
{%highlight kotlin%}
package com.example.androidwebview

import android.content.Context
import android.os.Build
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.webkit.JavascriptInterface
import android.webkit.WebChromeClient
import android.webkit.WebView
import android.webkit.WebViewClient
import android.widget.Toast
import androidx.annotation.RequiresApi

class WebAppInterface (private val mContext: Context) {
    @JavascriptInterface
    fun showToast(toast: String) {
        Toast.makeText(mContext, toast, Toast.LENGTH_SHORT).show()
    }
}

class MainActivity : AppCompatActivity() {

    @RequiresApi(Build.VERSION_CODES.KITKAT)
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val webView: WebView = findViewById(R.id.webView)
        webView.settings.javaScriptEnabled = true
        webView.webViewClient = CustomWebViewClient()
        webView.webChromeClient = WebChromeClient()
        webView.addJavascriptInterface(WebAppInterface(this), "Android")
        webView.loadUrl("http://192.168.0.102:3000")

    }
    private inner class CustomWebViewClient : WebViewClient() {
        override fun onPageFinished(view: WebView, url: String) {
            view.loadUrl("javascript:__SWIFT_CALL__.setBackgroundColor()")
            view.loadUrl("javascript:__SWIFT_CALL__.setTextColor('red')")
        }
    }
}
{%endhighlight%}

- WebAppInterface : 
    - webview에서 native로 실행시킬 수 있는 interface 함수를 정의한 클래스 입니다.
    - 여기서는 toast를 통해 webview에서 받아온 메시지를 띄워줍니다.
- WebViewClient :
    - 웹뷰에서의 발생하는 다양한 요청이나 상태 에러 등 다양한 상황에서의 콜백을 조작할 수 있습니다.
    - 다양한 생명주기 메서드가 있으며, 웹뷰 생명주기에 따라 다양한 상황 별 행위를 정의할 수 있습니다.
    - 여기서는 웹뷰의 로드가 완료된 후 webview에 정의된 메서드를 정의하는 역할을 하는 custom WebViewClient class를 정의하고 사용했습니다.
- WebChromeClient :
    - WebViewClient 비슷한 역할을 하지만, chrome에 특화된 역할을 합니다.
- WebView에 정의된 js 코드 호출 :
    - 웹 페이지 로드 후 view.loadUrl("javascript:__SWIFT_CALL__.setTextColor('red')") 와 같이 호출할 수 있습니다.
    - 버전에 따라 loadUrl 혹은 evaluateJavascript 를 사용할 수 있습니다.        

## webView 코드 작성
### index.html

{%highlight html%}
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <link rel="icon" href="%PUBLIC_URL%/favicon.ico" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <meta name="theme-color" content="#000000" />
    <meta
      name="description"
      content="Web site created using create-react-app"
    />
    <link rel="apple-touch-icon" href="%PUBLIC_URL%/logo192.png" />
    <!--
      manifest.json provides metadata used when your web app is installed on a
      user's mobile device or desktop. See https://developers.google.com/web/fundamentals/web-app-manifest/
    -->
    <link rel="manifest" href="%PUBLIC_URL%/manifest.json" />
    <!--
      Notice the use of %PUBLIC_URL% in the tags above.
      It will be replaced with the URL of the `public` folder during the build.
      Only files inside the `public` folder can be referenced from the HTML.

      Unlike "/favicon.ico" or "favicon.ico", "%PUBLIC_URL%/favicon.ico" will
      work correctly both with client-side routing and a non-root public URL.
      Learn how to configure a non-root public URL by running `npm run build`.
    -->
    <script type="text/javascript">
      window["__SWIFT_CALL__"] = {}
    </script>
    <title>React App</title>
  </head>
  <body>
    <noscript>You need to enable JavaScript to run this app.</noscript>
    <div id="root">
    </div>

    <!--
      This HTML file is a template.
      If you open it directly in the browser, you will see an empty page.

      You can add webfonts, meta tags, or analytics to this file.
      The build step will place the bundled scripts into the <body> tag.

      To begin the development, run `npm start` or `yarn start`.
      To create a production bundle, use `npm run build` or `yarn build`.
    -->
  </body>
</html>

{%endhighlight%}

### index.js
{%highlight javascript%}
import React from "react";
import ReactDOM from "react-dom";
import "./index.css";
import App from "./App";

window["__SWIFT_CALL__"] = {
...window["__SWIFT_CALL__"],
setBackgroundColor: function () {
    document.body.style.backgroundColor = 'green'
}
}
ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById("root")
);

{%endhighlight%}

### App.js

{%highlight javascript%}
import React from "react";
import "./App.css";
import { isIos } from "./utils/userAgent";


window["__SWIFT_CALL__"] = {
    ...window["__SWIFT_CALL__"],
    setTextColor:function (color) {
       document.body.style.color = color
    }
}

function App() {

  const send = () => {
    if (isIos()) {
      window.webkit.messageHandlers.toast.postMessage("Hello WebKit");
    }
    if (isAndroid()) {
       window.Android.showToast("Hello Android")
     }
  };

  React.useState(() => {
      window["__SWIFT_CALL__"] = {
          ...window["__SWIFT_CALL__"],
          setJwt:function (jwt) {
              localStorage.setItem("jwtToken", jwt);
              return jwt;
          }
      }
  });


  return (
    <div style={{ backgroundColor: "blue", height: '300px',marginTop: '20px' }} className="App">
      <div>TEST2</div>

      <button style={{ color: "white" }} onClick={send}>
        메시지 발송
      </button>
    </div>
  );
}

export default App;
{%endhighlight%}

### utils/userAgent.js
{%highlight javascript%}
export const isAndroid = () => !!navigator?.userAgent?.match(/Android/i);
export const isIos = () => !!navigator?.userAgent?.match(/iPhone|iPad|iPod/i);
export const isMobile = () => isAndroid() || isIos();

{%endhighlight%}

#### native call
- window에 닿을 수 있는 곳에 native가 call할 수 있는 함수를 정의합니다.
    - ex) 
    - index.html의 script
    - 함수 스코프 내외
    - useState 작동 시점
- html render 후 해당 함수가 존재해야 합니다.
- 해당 함수가 존재하지 않는다면 undefined error 발생.
     
### webview call
- ex ) window.Android.showToast("Hello Android")
- kotlin에서 정의된 webView.addJavascriptInterface(WebAppInterface(this), "Android") 인터페이스 Android를 통해 호출
- Android에 매핑된 WebAppInterface 클래스에서 정의된 showToast를 호출

## 실행화면
<img src="{{site.baseurl}}/images/Android/androidExecuteBackground.png"/>

- setBackgroundColor : 배경화면이 초록색으로 변한것을 확인할 수 있습니다.
- setTextColor : TEST2 라는 텍스트가 빨강색인 것을 확인할 수 있습니다.

<img src="{{site.baseurl}}/images/Android/androidShowToast.png"/>

- `메시지 발송` 버튼 클릭 시 토스트가 발생하는 것을 확인할 수 있습니다.


    
<pre class="source">
출처:
- https://readystory.tistory.com/181

git example : 
- https://github.com/JongHyuckLee/webviewStudy
 
</pre>    