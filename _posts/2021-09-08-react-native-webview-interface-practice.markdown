---
layout: post
title:  "[Webview]React-native & webview 연결하기"
date:   2021-09-08
tags:   React-native, webview, react, javascript interface
---
# React-native, Webview, js interface 
<pre class="info-panel">
 - React-native 를 통해 웹뷰와 javascript interface에 대해 알아보도록 하겠습니다.
</pre>

# React-native 설치 및 실행 
{%highlight terminal%}
$ sudo npm install -g expo-cli
$ expo init MyProject
$ cd MyProject
$ npm start
{%endhighlight%}

<img src="{{site.baseurl}}/images/ReactNative/installReactNative.png"/>

- 왼쪽 메뉴에서 `Run On iOS simulator` 클릭<br>
<img src="{{site.baseurl}}/images/ReactNative/menuBar.png"/>

- Ios simulator 실행 <br>
<img src="{{site.baseurl}}/images/ReactNative/iosSimuator.png"/>

# React-native-webview 설치 
{%highlight terminal%}
$ npm install react-native-webview
{%endhighlight%}

# Webview 용 페이지 작성하기
- create-react-app을 통해 react app project를 만듭니다.
{%highlight terminal%}
$ npx create-react-app my-webview-app
$ cd my-webview-app
$ yarn start
{%endhighlight%}

- webview용 페이지 작성하기
{%highlight react%}
import React from 'react';
import './App.css';


function App() {
   
  return (
    <div className="App">
      <header className="App-header">
        Test
      </header>
    </div>
  );
}

export default App;

{%endhighlight%}

- react-native app에 webview 작성하기
{%highlight react%}
//MyProject > App.js
import { WebView } from "react-native-webview";
import React from "react";

export default function App () {
    const webviewRef = React.useRef(null);

    return (
        <WebView
            domStorageEnabled={true}
            sharedCookiesEnabled={true}
            originWhitelist={["*"]}
            renderLoading={() => <></>}
            ref={webviewRef}
            source={{ uri: 'http://localhost:3000/' }}
            javaScriptEnabled={true}

        />
    );
};

{%endhighlight%}

- 아래와 같이 react-native-app에 cra를 통해 띄운 웹페이지가 뜨는 것을 볼 수 있습니다.
 <img src="{{site.baseurl}}/images/ReactNative/craWebView.png"/>
 
<pre class="source">
출처:
-  https://reactnative.dev/docs/environment-setup
- https://github.com/react-native-webview/react-native-webview/blob/master/docs/Reference.md
git example :
 
 
</pre>