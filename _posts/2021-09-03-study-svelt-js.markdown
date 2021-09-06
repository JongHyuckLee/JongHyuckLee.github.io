---
layout: post
title:  "[Svelte]Svelte 기초"
date:   2021-09-03
tags:   Start Svelte.js
---
# Svelte.js 알아보기
<pre class="info-panel">
 - 2021년 가장 사랑받는 웹 프레임워크
 - 늘 그래왔듯이 React와 Vue가 선두를 달리고 있지만, Svelte는 빠르게 선두 자리를 차지하기 위한 매우 진지한 경쟁자로 자리매김하고 있습니다.
 - 점점 점유율을 높여가며 성장하는 프레임워크인 Svelte.js에 대해 알아보도록 하겠습니다.
 - 이 포스트의 모든 코드는 https://github.com/JongHyuckLee/svelte-study 를 기반으로 합니다.
</pre>

## 1. Svelte.js의 장점
#### 1. 적은 러닝 커브 
- 이미 알고 있는 vanilla js, HTML, css를 통해 작성 가능.

{%highlight html%}
# App.svelte
<script>
	export let name;
</script>

<main>
	<h1>Hello {name}!</h1>
	<p>Visit the <a href="https://svelte.dev/tutorial">Svelte tutorial</a> to learn how to build Svelte apps.</p>
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>
{%endhighlight%}


#### 2. 코드량 감소
- React.js나 Vue.js에 비해 보일러플레이트 코드가 작음.
{%highlight React%}
// React

import React from 'react'

export default function Index() {
    const [name] = React.useState('React');

    return (
        <main>
            <h1 style={{color: "#00D8FF"}}>Hello {name}</h1>
            <p>Visit the {name}</p>
        </main>
    )

}
{%endhighlight%}

{%highlight Vue%}
// Vue.js
<script>
export default {
  data: function () {
    return {
      name: 'Vue',
    }
  }
}
</script>

<template>
  <main>
    <h1>Hello {{name}}</h1>
    <p>Visit the {{name}}!!.</p>
  </main>
</template>
{%endhighlight%}

{%highlight javascript%}
// Svelte
<script>
	let name = 'Svelte';
</script>

<main>
	<h1>Hello {name}!</h1>
	<p>Visit the Svelte!!</p>
</main>
{%endhighlight%}

#### 3. Virtual Dom을 사용하지 않음.
- React, Vue의 경우 virtual dom을 통해 메모리에 계산된 마지막 dom tree를 현재의 dom과 `비교`하여 적용합니다.
- virtual dom을 사용하는 측에서는 이는 real dom에서 re-paint, re-flow가 일어나는 것에 비하면 훨씬 나은 오버헤드가 적용된다고 하지만, virtual-dom과 real-dom사이를 비교 & 적용하는 것은 적지 않은 오버헤드가 발생합니다.
- Framework 경량화를 통해 프론트를 더 빠르게 시작하고 유지할 수 있습니다.
- svelte 공식 문서에서는 어떤 부분이 변화할지 안다고 기술 되어있습니다. svelte에서 관리하는 state를 바탕으로 Observable pattern을 통해 변화된 부분만 리렌더링하는 것으로 보입니다.

#### 4. Reactive
- React, Vue와 같이 데이터 변경에 따른 화면 구성을 합니다.
- 즉, 상태 관리에 따른 반응형 화면을 구성합니다.

## 2. 설치하기
<pre class="info-panel">
 - 아래의 설명 예제는 기본 svelte 설치 script입니다.
 - react의 create-react-app과 같은 역할입니다.
 - 하지만 제가 작성한 example code는 svelte, react, vue의 차이점을 보여주기 위해 webpack 설정을 직접 구성했습니다.
</pre>
- bundling tool : roll up
{%highlight shell%}
// sveltejs/template으로 svelte 환경을 구성하면 bundle tool이 roll up으로 설치됩니다.
npx degit sveltejs/template my-svelte-project

npm install
npm run dev
{%endhighlight%}

- bundling tool : webpack
{%highlight shell%}
npx degit sveltejs/template-webpack my-svelte-project

npm install
npm run dev
{%endhighlight%}

## 3. 파일 구성
- React의 경우 html 코드를 jsx(js code)로 작성하는 만큼, .jsx의 내부는 js 코드로 모든 파일이 이루어져 있습니다.
    - html : jsx(js) 내부에 html 태그 작성, 스코프 내의 선언된 변수 및 함수, global 객체에 접근할 수 있습니다.
    - js 코드 : class based component 또는 function based component에서 모두 스코프 내부에 변수 및 함수 코드를 작성. 
    - style을 import하거나 inline-style을 이용합니다.
    - style-component, sass 등을 이용할 수도 있습니다.
- Vue의 경우 <template></template>, <script></script>, <style></style>의 세개의 섹션으로 영역이 구성됩니다.
    - html : <template></template> 내부에 html tag로 작성. 
        - directive 혹은 지시어를 통해 Vue instance 내부의 속성을 사용할 수 있습니다.
        - directive를 통해 조건문, 제어문을 사용.
        - Vue instance가 아닌 global 객체에 접근할 수 없습니다.
    - js 코드 : <script></script> 내부 Vue instance 내부에 관련 코드 작성.
    - style : 지역 style의 경우 <style></style> 태그 내부에 작성.
- Svelte의 경우 html tag, <script></script>, <style></style>의 영역으로 구성됩니다.
    - html: wrapper html 태그를 이용해 html 작성 
        - ex) <div>...</div> , <main></main>
        - template 내부 약속어를 통해 조건문, 제어문을 사용.
        - html 태그내에서 <script></script>에서 정의된 변수, 함수, 글로벌 객체에 접근 가능.
    - js 코드: <script></script> 태그내에서 vanillaJs와 같이 변수 & 함수 초기화
    - style : 지역 style의 경우 <style</style> 태그내에서 사용.

{%highlight React%}
//React
import React from 'react'
export default function Index() {
    const [name] = React.useState('React');

    return (
        <main>
            <h1 style={{color: "#00D8FF"}}>Hello {name}</h1>
            <p>Visit the {name}</p>
        </main>
    )

}
{%endhighlight%}
{%highlight Vue%}
//Vue
<script>
export default {
  data: function () {
    return {
      name: 'Vue',
    }
  },
  methods: {
   
  }
}
</script>

<template>
  <main>
    <h1>Hello {{name}}</h1>
<!--    <p @click="()=>console.log('click Vue')">Visit the {{name}}!!.</p> Vue instance 외부 global에 접근할 수 없음.-->
    <p>Visit the {{name}}!!.</p>
  </main>
</template>

<style>
main {
  text-align: center;
  padding: 1em;
  max-width: 240px;
  margin: 0 auto;
}

h1 {
  color: #47C83E;
  text-transform: uppercase;
  font-size: 4em;
  font-weight: 100;
}
input {
  margin-bottom: 10px;
  margin-top: 20px;
}

@media (min-width: 640px) {
  main {
    max-width: none;
  }
}
</style>
{%endhighlight%}
{%highlight js%}
<script>
	let name = 'Svelte';
</script>

<main>
	<h1>Hello {name}!</h1>
	<p on:click={() => console.log("click Svelte")}>Visit the Svelte!!</p>
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}
	input {
		margin-bottom: 10px;
		margin-top: 20px;
	}


	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>

{%endhighlight%}
<pre class="source">
출처 :
 - https://2020.stateofjs.com/ko-KR/technologies/front-end-frameworks/
 - https://velog.io/@katanazero86/svelte-life-cycle-%EC%83%9D%EB%AA%85%EC%A3%BC%EA%B8%B0
git example :
 - https://github.com/JongHyuckLee/svelte-study
 
</pre>