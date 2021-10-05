---
layout: post
title: "[Middleware] Graphql 사용해 보기"
date: 2021-10-06
tags: Graphql, client, react, middleware
---

# Graphql 사용해보기
<pre class="info-panel">
 - Graphql을 사용해 Server - Client 간의 MiddleWare를 구성해보겠습니다.
 - 본 글의 Graphql 환경은 node.js - Express.js를 통해 환경을 구성하겠습니다.
 - Client 환경은 React를 통해 구성하겠습니다.
</pre>

# graphql server 설정하기 
## server.js

{%highlight node%}
const express = require('express');
const { graphqlHTTP } = require('express-graphql');
const { buildSchema } = require('graphql');
const cors = require('cors');

// Construct a schema, using GraphQL schema language
const schema = buildSchema(`
  type Query {
    helloWorld: String
  }
`);

const rootValue = {
    helloWorld: () => {
        return 'Hello world!';
    },
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

- Exppress를 통해 서버 환경을 구성했습니다.
- `express-graphql` 과 `graphql`를 통해 graphql의 스키마와 환경을 설정할 수 있습니다.
- buildSchema 를 통해 schema를 설정했습니다.
- graphqlHTTP를 통해 graphql의 endpoint를 설정했습니다.

# client 설정하기
## App.js

{%highlight javascript%}
import axios from 'axios'
import React from 'react';

function App() {
  const [graphqlData, setGraphqlData] = React.useState('');

  React.useEffect(() => {
    (async () => {
      const result = await axios.post('http://localhost:4000/graphql',  {
        query: `{helloWorld}`
      });
      setGraphqlData(JSON.stringify(result?.data));
    })()
  }, [])
  return (
    <div className="App">
      <div id="data">{graphqlData}</div>
    </div>
  );
}

export default App;

{%endhighlight%}

- `await axios.post('http://localhost:4000/graphql'...` 을 통해 graphql에서 데이터를 요청합니다.
- graphql을 get으로 요청하기 위해서는 query string으로 요청해야합니다.
- 하지만 본 스터디에서는 보다 직관적인 요청 예제를 보이기 위해 post를 사용했습니다.
- 아래와 같이 get query string으로 요청할 수 있습니다.

{%highlight javascript%}

import axios from 'axios'
import React from 'react';

function App() {
  const [graphqlData, setGraphqlData] = React.useState('');

  React.useEffect(() => {
    (async () => {
      const result = await axios.get('http://localhost:4000/graphql',  {
        params: {
          query: "{helloWorld}"
        }
      });
      setGraphqlData(JSON.stringify(result?.data));
    })()
  }, [])
  return (
    <div className="App">
      <div id="data">{graphqlData}</div>
    </div>
  );
}

export default App;

{%endhighlight%}


# 실행 화면

<img src="{{site.baseurl}}/images/startGraphql.png"/>

- graphql에서 받은 데이터를 화면에 표시해주는것을 볼 수 있습니다.

<pre class="source">
출처:
- https://graphql.org/graphql-js/running-an-express-graphql-server/

git example : 
- https://github.com/JongHyuckLee/graphqlStudy
 
</pre>    
