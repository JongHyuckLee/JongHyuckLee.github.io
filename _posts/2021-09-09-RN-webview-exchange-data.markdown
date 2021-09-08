---
layout: post
title:  "[Webview]React-native & webview 데이터 연동"
date:   2021-09-09
tags:   React-native, webview, react, postMessage
---
# React-native & webview 데이터 연동 
<pre class="info-panel">
 - RN과 webview간에 데이터를 주고 받는 방법에 대해 알아보도록 하겠습니다.
 - RN과 webview와의 연동을 자세히 알고 싶으시다면 이전 블로그 글을 참조하시기 바랍니다.
 - https://jonghyucklee.github.io/2021/09/08/react-native-webview-interface-practice/
</pre>

# RN에 webview와 데이터 연동을 위한 코드 작성
{%highlight react%}
import { WebView } from "react-native-webview";
import React from "react";

export default function App () {
    const injectCode = `window.__TEST__ = 'global test'`

    const webviewRef = React.useRef(null);
    const handleOnMessage = ({ nativeEvent: { data } }) => {
        console.log(data)
    }
    const handleOnLoadEnd = () => {
        webviewRef.current.postMessage("from RN to Webview")
    }

    return (
        <WebView
            domStorageEnabled={true}
            sharedCookiesEnabled={true}
            originWhitelist={["*"]}
            renderLoading={() => <></>}
            onLoadEnd={handleOnLoadEnd}
            onMessage={handleOnMessage}
            ref={webviewRef}
            source={{ uri: 'http://localhost:3000/' }}
            javaScriptEnabled={true}
            injectedJavaScript={injectCode}
        />
    );
};

{%endhighlight%}

## attribute 설명
- domStorageEnabled : Dom Storage를 사용 가능하도록 합니다. 안드로이드에서만 사용 가능합니다.
- sharedCookiesEnabled : 
    - [NSHTTPCookieStorage sharedHTTPCookieStorage] 에 정의된 쿠키를 공유할 수 있습니다. 
    - 웹뷰에서 모든 request에서 사용할 수 있습니다.
    - default value는 false입니다.
- originWhitelist:
    - 허용 navigate의 목록입니다.
    - ex) ['https://*', 'git://*']
    - *은 모든 navigate에 대한 허용입니다.
    - 유저가 navigate를 탭했을 때 whitelist에 존재하지 않는다면 그 url은 os에 의해 handling됩니다.
    - default whitelist는 "http://" and "https://" 입니다.
- onLoadEnd:
    - webview가 로드가 성공혹은 실패됐을 때 트리거 되는 function입니다.
    - webview가 렌더링될 때 초기 data를 보내고 싶다면 이곳에서 data를 전송하면 됩니다.
- onMessage:
    - webview 쪽에서 window.ReactNativeWebView.postMessage를 call하면 onMessage가 트리거됩니다.
    - 이러한 설정은 webview의 global variable에 주입됩니다. 
    - window.ReactNativeWebView.postMessage에는 data 하나의 argument가 할당됩니다.
    - event object를 받고 싶다면 `event.nativeEvent.data`를 이용합니다.
    - data는 string입니다.
- source:
    - webview에 로드되는 정적 HTML 또는 URI입니다.
    - 정적 HTML이라면 whitelist에 ["*"] 를 등록해야 합니다.
- javaScriptEnabled:
    - Webview 내에서 Javascript를 사용할 수 있는지 여부에 대한 flag입니다.
    - default는 true입니다. 
- injectedJavaScript   
    - Dom이 완성된 뒤 webview에서 사용할 수 있는 JS코드를 삽입하는 attribute입니다.
    
# webview 코드 작성        
{%highlight react%}
import React from 'react';
import './App.css';


function App() {
    function send() {
        console.log(window.__TEST__)
        window?.ReactNativeWebView?.postMessage('From Webview to RN');
    }
    React.useEffect(() => {
        window.addEventListener("message",(e) => {
            alert(e.data)
        })
    }, [])
  return (
    <div className="App">
      <header className="App-header">
        Test
          <button onClick={send}>Send</button>
      </header>
    </div>
  );
}

export default App;

{%endhighlight%}

# 코드 설명
- ReactNativeWebView : 공식문서에서 찾진 못했지만 RN에서 주입된 코드라고 생각됩니다...
- Window:message event
    - message 이벤트는 window 객체가 window 메세지를 받았을 때 trigger됩니다.
- window.postMessage() 메소드는 Window 오브젝트 사이에서 안전하게 cross-origin 통신을 할 수 있게 합니다. 예시로, 페이지와 생성된 팝업 간의 통신이나, 페이지와 페이지 안의 iframe 간의 통신에 사용할 수 있습니다.

# 동작 설명
- webview가 load되며 "from RN to Webview" 메세지가 alert창이 뜹니다.
    - webview onLoad에 등록된 handleOnLoadEnd function 때문입니다.
{%highlight react%}
    const handleOnLoadEnd = () => {
        webviewRef.current.postMessage("from RN to Webview")
    }

{%endhighlight%}
<img src="{{site.baseurl}}/images/ReactNative/onLoad.png"/>

- `send` 버튼을 클릭하면 RN 콘솔 창에 'From Webview to RN' 메시지가 뜹니다.
    - `window?.ReactNativeWebView?.postMessage('From Webview to RN');` 코드 때문입니다.
{%highlight react%}
    function send() {
        console.log(window.__TEST__)
        window?.ReactNativeWebView?.postMessage('From Webview to RN');
    }
{%endhighlight%}
<img src="{{site.baseurl}}/images/ReactNative/postMessage.png"/>

- `send` 클릭 시에 webview console창에 global test 메시지가 출력됩니다.
    - RN 측에서 js 코드를 inject 했기 때문입니다.
    
{%highlight react%}
//RN
const injectCode = `window.__TEST__ = 'global test'`;
injectedJavaScript={injectCode}

//webview
console.log(window.__TEST__)
{%endhighlight%}

<img src="{{site.baseurl}}/images/ReactNative/injectCode.png"/>

<pre class="source">
출처:
- https://github.com/react-native-webview/react-native-webview/blob/master/docs/Reference.md#injectedjavascript
- https://developer.mozilla.org/ko/docs/Web/API/Window/postMessage
git example : 
- https://github.com/JongHyuckLee/webviewWhitRNAndCra
 
</pre>