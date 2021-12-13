---
layout: post
title: "[Websocket] Websocket 기초 따라해보기"
date: 2021-12-13
tags: websocket, client, server
---

# Websocket 개념 이해
- 웹 소켓은 TCP 연결에 이중 통신 채널을 제공하는 컴퓨터 통신 프로토콜입니다.
- 웹 소켓 프로토콜은 웹 브라우저와 웹 서버 간의 통신을 허용
- http 프로토콜과 같은 반 이중 모드에 비해 서버와 실시간 데이터 전송을 쉽게함.
- http와 달리 웹 소켓은 완전한 이중 통신을 사용하여 웹 소켓이 TCP에서 메시지를 스트리밍할 수 있음.
- 웹 소켓 프로토콜 사양은 ws(WebSocket), wss(WebSocket Secure)의 두 가지로 각각 암호화되지 않은 연결 및 암호화된 연결에 사용됨.

# Web Socket Client 작성해 보기.
- ws 프로토콜을 이용해 client에서 server로 연결하는 간단한 예시입니다.
- websocket 연결과 메시지 구독 예시를 알아봅니다.
- 출처 : https://poiemaweb.com/nodejs-socketio

{%highlight html%}

<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>WebSocket Example</title>
</head>
<body>
<script>
    const ws = new WebSocket("ws://localhost:3000");

    // 연결이 수립되면 서버에 메시지를 전송한다
    ws.onopen = function() {
        ws.send("Client message: Hi!");
    }

    // 서버로 부터 메시지를 수신한다
    ws.onmessage = function(event) {
        console.log("Server message: ", event.data);
    }

    // error event handler
    ws.onerror = function(event) {
        console.log("Server error message: ", event.data);
    }
</script>
</body>
</html>

{%endhighlight%}

# Web Socket Server 작성해 보기.
- server는 노드로 작성해보도록 하겠습니다.
- 먼저 프로젝트를 생성하고 관련 package를 설치합니다.
- 출처 : https://poiemaweb.com/nodejs-socketio

## 디렉토리 생성 및 관련 package 설치

- root directory 생성
- install package

{%highlight terminal%}

$ mkdir basic-websocket && cd basic-websocket
$ npm init --y
$ npm install ws --save

{%endhighlight%}

## 코드 작성

- server 코드를 작성합니다.
- server.js

{%highlight js%}

const WebSocketServer = require("ws").Server;
const wss = new WebSocketServer({ port: 3000 });

// 연결이 수립되면 클라이언트에 메시지를 전송하고 클라이언트로부터의 메시지를 수신한다
wss.on("connection", function(ws) {
    ws.send("Hello! I am a server.");
    ws.on("message", function(message) {
        console.log("Received: %s", message);
    });
});

{%endhighlight%}

# Server 실행하기

{%highlight terminal%}

$ node server

{%endhighlight%}

# 실행화면
- local에서 실행한 index.html의 콘솔창에서 websocket 연결 시 init 메세지가 찍히는것을 볼 수 있습니다.

<img src="{{site.baseurl}}/images/WebSocket/basic-websocket.png"/>


<pre class="source">
출처:
- https://urmaru.com/7
- https://poiemaweb.com/nodejs-socketio

git example : 
- https://github.com/JongHyuckLee/websocketStudy
 
</pre>    