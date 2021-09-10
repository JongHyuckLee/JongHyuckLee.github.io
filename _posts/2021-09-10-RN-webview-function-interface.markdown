---
layout: post
title: "[Webview]React-native & webview function interface 구축하기"
date: 2021-09-10
tags: React-native, webview, react, javascript interface, execute function, 리액트, 웹뷰, 자바스크립트
---

# React-native, Webview, js execute function 
<pre class="info-panel">
 - 웹뷰에서 트리거된 javascript postMessage로 React Native의 function실행에 대해 대해 알아보도록 하겠습니다.
 - postMessage에 대한 예제를 보고 싶으시다면 이전 글을 보시면 됩니다.
 - http://127.0.0.1:4000/2021/09/09/RN-webview-exchange-data/
 - 여기있는 모든 예제는 https://github.com/JongHyuckLee/webviewWhitRNAndCra 에서 확인할 수 있습니다.
</pre>

## 1. React Native 코드
- App.js, interface/index.js, data/colors.json 코드를 작성합니다.
- 아래는 각 예제 입니다.

### App.js
{%highlight react%}
import { WebView } from "react-native-webview";
import React from "react";
import Interface from "./interfaces/index"
import { Button } from 'react-native-elements';

export default function App () {
    const [backgroundColor, setBackgroundColor] = React.useState('#0f0')
    const injectCode = `window.__TEST__ = 'global test'`

    const webviewRef = React.useRef(null);
    const handleOnMessage = React.useCallback(async ({ nativeEvent: { data } }) => {
        if (Interface[data]) {
            await Interface[data]?.(setBackgroundColor);
        }
    }, [])
    const handleOnLoadEnd = () => {
        webviewRef.current.postMessage("from RN to Webview")

    }

    return (
        <>
        <WebView
            containerStyle={  {
                width: '50%',
                alignSelf: 'center',
                alignContent: 'center',
                borderRadius: 15,
                paddingTop: 100,
                paddingBottom: 100,
            }}
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
            <Button title="Native Button" buttonStyle={{backgroundColor}} />
        </>
    );
};

{%endhighlight%}

- webview에서 ReactNativeWebView를 통해 발송된 메시지 받습니다.

 {%highlight react%}
 const handleOnMessage = async ({ nativeEvent: { data } }) => {
         if (Interface[data]) {
             const result = await Interface[data]?.();
             console.log(result[0]?.color)
         }
  }
     
  onMessage={handleOnMessage}
 {%endhighlight%}
 - React Native에 등록된 함수라면 실행시킵니다.
 {%highlight react%}
  if (Interface[data]) {
      const result = await Interface[data]?.();
      console.log(result[0]?.color)
   }
{%endhighlight%}

### interfaces/index.js
- React Native에 등록된 함수입니다.
{%highlight react%}
import {random} from 'lodash';

const Interface = {
    setColor: async (callback) => {
         let colors = await import('../data/colors.json');

         callback(colors?.[random(0, colors?.default?.length)]?.value)
    }
}

export default Interface;
{%endhighlight%}

### data/colors.json성
- 불러올 데이터입니다.
{%highlight json%}
[
  {
    "color": "red",
    "value": "#f00"
  },
  {
    "color": "green",
    "value": "#0f0"
  },
  {
    "color": "blue",
    "value": "#00f"
  },
  {
    "color": "cyan",
    "value": "#0ff"
  },
  {
    "color": "magenta",
    "value": "#f0f"
  },
  {
    "color": "yellow",
    "value": "#ff0"
  },
  {
    "color": "black",
    "value": "#000"
  }
]
{%endhighlight%}
- 파일을 aysnc/await를 통해 비동기로 불러옵니다.
- 받아온 데이터를 return 합니다.

### 설명
- webview에서 RN으로 특정 메시지를 전송합니다.
- RN에 메시지에 매핑되는 함수가 있다면 불러와 호출합니다.

## 2. WebView 코드 작성
### src/App.js

{%highlight react%}
import React from 'react';
import './App.css';


function App() {
    function send() {
        console.log(window.__TEST__)
        window?.ReactNativeWebView?.postMessage('From Webview to RN');
    }

    function setColor() {
        window?.ReactNativeWebView?.postMessage('setColor');
    }
    React.useEffect(() => {
        window.addEventListener("message",(e) => {
            console.log(e.data)
        })
    }, [])
  return (
    <div className="App">
      <header className="App-header">
        Test
          <button onClick={send}>Send</button>
          <button onClick={setColor}>Set Colors</button>
      </header>
    </div>
  );
}

export default App;
{%endhighlight%}

- postMessage를 통해 RN에 메시지를 전달합니다.
- `Set Colors` 버튼을 클릭하면 WebView로 `setColor`라는 string data가 React Native로 전달됩니다.

{%highlight react%}
    function setColor() {
        window?.ReactNativeWebView?.postMessage('setColor');
    }
   <button onClick={setColor}>Set Colors</button>
{%endhighlight%}

## 3. 전체 설명
- WebView에서 `Set Colors` 버튼을 클릭합니다.
- WebView에서 React Native로 `setColor`라는 메시지가 전송됩니다.
- React Native는 `setColor`라는 메시지를 받습니다.
- React Native에서는 `interface`라고 정의된 함수 목록에 전송받은 메시지가 있는지 체크합니다.
- 매핑되는 함수가 있다면 실행합니다.
- 여기서는 setColor가 실행되며 RN에 등록된 데이터 colors.json 중의 하나의 데이터가 랜덤으로 매핑됩니다.
- RN 버튼의 색이 랜덤으로 변합니다.

## 4. 실행화면 
<h4>`Set Colors` 버튼 클릭 전</h4>
 <img src="{{site.baseurl}}/images/ReactNative/beforeClick.png"/>
 
<h4>`Set Colors` 버튼 클릭 후</h4>
  <img src="{{site.baseurl}}/images/ReactNative/afterClick.png"/>

<pre class="source">
git example : 
- https://github.com/JongHyuckLee/webviewWhitRNAndCra
 
</pre>