---
layout: post
title: "[Middleware] Graphql 미들웨어로 사용해보기"
date: 2021-10-10
tags: Graphql, client, react, middleware
---

# Graphql 사용해보기
<pre class="info-panel">
 - Graphql을 사용해 Server - Client 간의 MiddleWare를 구성해보겠습니다.
 - 본 글의 Graphql 환경은 node.js - Express.js를 통해 환경을 구성하겠습니다.
 - Client 환경은 React를 통해 구성하겠습니다.
</pre>

# server.js

{%highlight node%}
const express = require('express');
const { graphqlHTTP } = require('express-graphql');
const { buildSchema } = require('graphql');
const cors = require('cors');
const POSTS = [
    { author: "John Doe", body: "Hello world" },
    { author: "Jane Doe", body: "Hi, planet!" },
];

const schema = buildSchema(`
   type Query {
    posts: [Post]
    post(id: ID!): Post
  }

  type Post {
    id: ID
    author: String
    body: String
  }
`);

const mapPost = (post, id) => post && ({ id, ...post });
const rootValue = {
    posts: () => POSTS.map(mapPost),
    post: ({ id }) => mapPost(POSTS[id], id),
};

const app = express();
const corsOptions = {
    origin: 'http://localhost:3000',
    credentials: true
}

app.use(cors(corsOptions));
app.use('/graphql', graphqlHTTP({
    schema,
    rootValue,
    graphiql: true,
}));
app.listen(4000);
console.log('Running a GraphQL API server at http://localhost:4000/graphql');
{%endhighlight%}

- POSTS: mock data 입니다.
- buildSchema 
    - posts: [Post] : 모든 posts 목록을 불러오는 Query입니다.
    - post(id: ID!): Post : posts 중 mapping되는 id를 불러오는 Query입니다.
-  rootValue
    - posts: () => POSTS.map(mapPost) : 모든 posts를 불러오는 로직입니다.
    -  post: ({ id }) => mapPost(POSTS[id], id) : id가 매핑되는 post를 불러오는 로직입니다.
    
# App.js    

{%highlight javascript%}
import axios from 'axios'
import React from 'react';

function App() {
  const [graphqlDataList, setGraphqlDataList] = React.useState([]);
  const [graphqlData, setGraphqlData] = React.useState('');

  React.useEffect(() => {
    (async () => {
      const result = await axios.get('http://localhost:4000/graphql',  {
        params: {
          query: `{
                    posts {
                            id,
                            author,
                            body
                          }
                  }`
        }
      });
      setGraphqlDataList(JSON.stringify(result?.data));
    })()
  }, [])

  React.useEffect(() => {
    (async () => {
      const result = await axios.get('http://localhost:4000/graphql',  {
        params: {
          query: `{
                    post(id: 1) {
                                  id,
                                  author,
                                  body
                    }
                  }`
        }
      });
      setGraphqlData(JSON.stringify(result?.data));
    })()
  }, [])
  return (
    <div className="App">
      <div>{graphqlDataList}</div>
      <div>{graphqlData}</div>
    </div>
  );
}

export default App;
{%endhighlight%}

- axios를 통해 모든 posts와 id가 매핑되는 post를 불러와 화면에 출력합니다.
- `post(id: 1) {` id에 특정 id를 입력하면 매핑되는 postㅡㄹㄹ 불러올 수 있습니다.

# 실행 화면

<img src="{{site.baseurl}}/images/MiddleWare/graphqlFiltering.png"/>

- graphql이 미들웨어로써 원본 데이터를 중간에서 조작해서 client단에 보내줄 수 있는 예제입니다.
- 전체 포스트, 특정 아이디에 매핑되는 포스트 등의 예제를 통해 미들웨어가 중간에서 어떤 데이터를 추가 조작해서 response할 수 있음을 알 수 있습니다.
- 만약 어떤 api나 database resource 에서 받아온 데이터를 클라이언트에 맞게 추가 조작하는 미들웨어로써 동작할 수 있습니다.
- 추가적으로 에러코드나 response 코드 추가와 같이 추가적인 response 데이터 작업을 수행할 수 있습니다.

<pre class="source">
출처:
- https://graphql.org/graphql-js/running-an-express-graphql-server/
- https://developer.okta.com/blog/2018/10/11/build-simple-web-app-with-express-react-graphql

git example : 
- https://github.com/JongHyuckLee/graphqlStudy
 
</pre>    

