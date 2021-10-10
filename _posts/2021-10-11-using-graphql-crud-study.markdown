---
layout: post
title: "[Middleware] Graphql 미들웨어로 Create & Update 해보기"
date: 2021-10-11
tags: Graphql, client, react, middleware
---

# Graphql 미들웨어로 Create & Update 해보기
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
   type Mutation {
     submitPost(input: PostInput!): Post,
     updatePost(input: PostInput!): Post
   }
    
   input PostInput {
     id: ID
     author: String!
     body: String!
   }
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
    submitPost: ({ input: { id, author, body } }) => {
        const post = { author, body };
        let index = POSTS.length;

        if (id != null && id >= 0 && id < POSTS.length) {
            if (POSTS[id].author !== author) return null;

            POSTS.splice(id, 1, post);
            index = id;
        } else {
            POSTS.push(post);
        }

        return mapPost(post, index);
    },
    updatePost: ({ input: { id, author, body } }) => {
        if (!POSTS[id]) {
            throw Error('id not in POSTS');
        }
        POSTS[id] = {
            author,
            body
        }
        return {id, ...POSTS[id]};
    }
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
{%highlight node%}

- Mutation : Create, Update 등 데이터 변경이 일어날 때 사용되는 Graphql을 정의합니다.
    -  submitPost : Create 역할을 하는 Graphql 입니다.
    -  updatePost : Update 역할을 하는 Graphql 입니다.
- rootValue: Create, Update 역할을 하는 메서드를 정의합니다.
    - submitPost : POSTS array에 원래 없던 데이터를 insert하는 역할을 합니다.
    - updatePost : 특정 id(index)에 데이터를 업데이트하는 역할을 합니다.
    
# App.js    

{%highlight javascript%}    
import axios from 'axios'
import React from 'react';

function App() {
  const [graphqlDataList, setGraphqlDataList] = React.useState([]);
  const [graphqlData, setGraphqlData] = React.useState('');
  const [graphqlCreate, setGraphqlCreate] = React.useState('');
  const [graphqlUpdate, setGraphqlUpdate] = React.useState('');
  const onCreateClickHandler = () => {
    (
       async () => {
         const result = await axios.post('http://localhost:4000/graphql', {
           query: ` mutation {
                          submitPost(input: {
                            author: "createTestAuthor",
                            body: "createTestBody"
                          }) {
                            id,
                            author,
                            body
                          }
                        }`
         });
         setGraphqlCreate(JSON.stringify(result?.data));
        }
    )()
  };

  const onUpdateClickHandler = () => {
      (
          async () => {
              const result = await axios.post('http://localhost:4000/graphql', {
                  query: ` mutation {
                          updatePost(input: {
                            id: 1,
                            author: "updateTestAuthor",
                            body: "updateTestBody"
                          }) {
                            id,
                            author,
                            body
                          }
                        }`
              });
              setGraphqlUpdate(JSON.stringify(result?.data));
          }
      )()
  }

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
  }, []);

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
  }, []);
  return (
    <div className="App">
      <div>{graphqlDataList}</div>
      <div>{graphqlData}</div>
      <button onClick={onCreateClickHandler}>Create Post</button>
      <div>{graphqlCreate}</div>
      <button onClick={onUpdateClickHandler}>Update Update</button>
      <div>{graphqlUpdate}</div>
    </div>
  );
}

export default App;

{%endhighlight%}        

- onCreateClickHandler : Create 용 비동기 request 메서드입니다.
    - graphqlCreate : return data가 출력됩니다.
- onUpdateClickHandler : Update 용 비동기 request 메서드입니다.
    - graphqlUpdate : return data가 출력됩니다.
    
# 실행 화면    

<img src="{{site.baseurl}}/images/MiddleWare/crudGraphql.png"/>

- graphql이 crud 의 중간 미들웨어로서 어떻게 동작할 수 있는지 공부해보았습니다.
- graphql은 db 혹은 엔드포이트와 client간의 미들웨어로써 중간에 필요한 동작을 해줄 수 있습니다.

<pre class="source">
출처:
- https://graphql.org/graphql-js/running-an-express-graphql-server/
- https://developer.okta.com/blog/2018/10/11/build-simple-web-app-with-express-react-graphql

git example : 
- https://github.com/JongHyuckLee/graphqlStudy
 
</pre>    


         
    