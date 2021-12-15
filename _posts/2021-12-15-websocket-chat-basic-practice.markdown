---
layout: post
title: "[Websocket] Websocket으로 기초적인 채팅 구현해보기"
date: 2021-12-15
tags: websocket, client, server, chat
---

# 시작
- 우선 구현 코드에 관한 코드를 기술한 후 이에 대한 설명을 진행하겠습니다.
- 구현 코드는 서버와 클라이언트 사이드로 구성하겠습니다.
- 각 코드 작성 후 그에 대한 설명을 작성하겠습니다.
- 각 코드의 참고 출처는 아래와 같습니다.
    - https://poiemaweb.com/nodejs-socketio
    - https://soonysoon.tistory.com/65

- 간단한 이벤트 설명
 - emit : 데이터 전송
 - on : 데이터 구독 
    
# SERVER

## 디렉토리 생성 및 패키지 설치

{%highlight terminal%}

$ mkdir websocket-server
$ cd websocket-server

$npm init --y
$npm install --save express
$npm install --save http
$npm install --save socket.io

{%endhighlight%}    

- express : express를 통해 노드 서버를 구축하겠습니다.
- http : http 통신을 위한 패키지입니다.
- socket.io : http에서 socket io를 이용하기 위함입니다.

## 코드 작성

- 자료 출처  : https://poiemaweb.com/nodejs-socketio

{%highlight js%}
const app = require('express')();
const server = require('http').createServer(app);
//http protocol server 객체 생성
const io = require('socket.io')(server,{
    cors : {
        origin :"*",
        credentials :true
    }
});
// socket으로 http 이용 및 cors 해제를 위한 옵션 

io.on('connection', function(socket) {
    socket.on('message',({name, message}) => {
        io.emit('message',({name: `from server ${name}`, message}))
    })
});

server.listen(4000, function() {
    console.log('Socket IO server listening on port 4000');
}); // 4000 port로 서버 생성
{%endhighlight%}

### 설명

{%highlight js%}
socket.on('message',({name, message}) => {
});
{%endhighlight%}

- 클라이언트로 부터 받은 메시지 
    - socket.on : 접속된 클라이언트에서 발송한 데이터를 구독합니다.    
    - 'message' : event name, 클라이언트가 메시지 송신 시 지정한 이벤트 명
    - ({name, message}) => { : 이벤트 핸들러, 핸들러 함수의 인자로 클라이언트가 송신한 메시지 전달.
    
{%highlight js%}
        io.emit('message',({name: `from server ${name}`, message}))
{%endhighlight%}
    
- 클라이언트로 보내는 메시지 
    - io.emit : 접속된 모든 클라이언트에게 메시지 전송.
    - socket.emit : 메시지를 전송한 클라이언트에게만 메시지 전송.
    - socket.broadcast.emit : 메시지를 전송한 클라이언트를 제외한 모든 클라이언트에게 메시지 전송.
    - io.to[id].emit : 특정 클라이언트에게만 메시지 전송.
    - 'message' : 클라이언트가 받는 이벤트 명
    - ({name: `from server ${name}`, message}) : client가 받을 메시지

# Client

## 디렉토리 생성 및 패키지 설치

{%highlight terminal%}

$ npx create-react-app websocket-client
$ cd websocket-client
$ npm install
$ npm install --save socket.io-client

{%endhighlight%}    

- socket client 설치

## 코드 작성

- 본 client 예제는 react로 작성됐습니다.
- 규격 html 태그 외는 styled 컴포넌트로 작성했습니다.
- 예제를 보기 위해서는 아래의 https://github.com/JongHyuckLee/websocketStudy 를 참고하시면 됩니다.

{%highlight js%}
import React, { useState, useEffect } from "react";
import io from "socket.io-client";
import { ColumnContainer, RowContainer } from "./styled";

const socket = io.connect("ws://localhost:4000");
const Chat = () => {
  const [name, setName] = useState("");
  const [message, setMessage] = useState("");
  const [chatList, setChatList] = useState([]);

  useEffect(() => {
    socket.on("message", ({ name, message }) => {
      setChatList((prevState) => [...prevState, { name, message }]);
    });
  }, []);

  const onSubmitHandler = () => {
    socket.emit("message", { name, message });
    setMessage("");
  };

  return (
    <ColumnContainer>
      <RowContainer>
        <input
          type="text"
          placeholder="채팅 이름 입력"
          onChange={(e) => setName(e.target.value)}
          value={name}
        />
        <input
          type="text"
          placeholder="채팅 메시지 입력"
          onChange={(e) => setMessage(e.target.value)}
          value={message}
        />
        <button onClick={onSubmitHandler}>메시지 보내기</button>
      </RowContainer>
      <ColumnContainer style={{ marginTop: "100px" }}>
        {chatList.map(({ name, message }, index) => (
          <RowContainer key={index}>
            {name} : {message}
          </RowContainer>
        ))}
      </ColumnContainer>
    </ColumnContainer>
  );
};
export default Chat;

{%endhighlight%}

## 설명

### 서버로 부터의 수신

{%highlight js%}
   socket.on("message", ({ name, message }) => {
      setChatList((prevState) => [...prevState, { name, message }]);
    });
{%endhighlight%}

- socket.on : 서버 구독
- "message" : event name 서버로 부터 구독할 이벤트 명
-  ({ name, message }) => { : 서버로부터 받을 파라미터

### 서버로 송신

{%highlight js%}
 socket.emit("message", { name, message });
{%endhighlight%}

- socket.emit : 서버로 전
- "message" : ev송ent name 서버로 전송할 이벤트 
-  ({ name, message }) => { : 서버로 전송할 파라미터


# 결과

<img src="{{site.baseurl}}/images/WebSocket/basic-chat.png"/>
    
<pre class="source">
출처:
- https://poiemaweb.com/nodejs-socketio
- https://soonysoon.tistory.com/65

git example : 
- https://github.com/JongHyuckLee/websocketStudy
 
</pre>    