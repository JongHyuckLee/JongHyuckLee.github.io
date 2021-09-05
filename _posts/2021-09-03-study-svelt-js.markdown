---
layout: post
title:  "[Svelte]Svelte 기초"
date:   2021-09-03
tags:   Svelte.js
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

#### 4. Reactive
- React, Vue와 같이 데이터 변경에 따른 화면 구성을 합니다.
- 즉, 상태 관리에 따른 반응형 화면을 구성합니다.

## 2. 상태 정의
- 상태 정의는 VanillaJs와 같이 변수 선언을 통해 상태를 정의합니다.

{%highlight React%}
// React
export default function Index() {
    const [name] = React.useState('React');
    const [inputValue, setInputValue] = React.useState("");
    const [list, setList] = React.useState(['item1', 'item2']);
    ....
}    
{%endhighlight%}

{%highlight Vue%}
// Vue
<script>
export default {
  data: function () {
    return {
      name: 'Vue',
      inputValue: '',
      list: ['item1', 'item2']
    }
  }
}
</script>
{%endhighlight%}

{%highlight javascript%}
// Svelte
<script>
	let name = 'Svelte';
	let inputValue = '';
	let list = ['item1', 'item2'];
</script>
{%endhighlight%}

## 3. method or function 정의.
- 내부 function or method의 정의도 vanillaJs와 같이 선언형으로 합니다.
{%highlight React%}
// React
export default function Index() {
    ....
    const onClick = () => {
        const value = inputRef?.current?.value;
        setList(prevState => [...prevState, value]);
    }
    ....
{%endhighlight%}

{%highlight Vue%}
// Vue
<script>
export default {
  ....
  methods: {
    onClick() {
      this.list.push(this.inputValue);
    }
  }
}
</script>
{%endhighlight%}

{%highlight javascript%}
//Svelte
<script>
	....
	function onClick() {
		list = [...list, inputValue];
	}
</script>
{%endhighlight%}

## 4. Props 전달 방식.
- 기존의 방식과 비슷하지만 간단 명료한 정의와 전달 방식을 갖고 있습니다.
- 컴포넌트에 key={value} 형식으로 전달.
- 사용 컴포넌트에서는 export keyword를 통해 props임을 명명.

{%highlight React%}
// React
// Index.jsx

import React from 'react'
import Item from "./Item";
export default function Index() {
    const [name] = React.useState('React');
    const [inputValue, setInputValue] = React.useState("");
    const [list, setList] = React.useState(['item1', 'item2']);
    const inputRef = React.useRef(null);
    const onClick = () => {
        const value = inputRef?.current?.value;

        setList(prevState => [...prevState, value]);
    }

    return (
        <main>
            <h1 style={{color: "#00D8FF"}}>Hello {name}</h1>
            <p>Visit the {name}</p>
            <input ref={inputRef} type="text" placeholder="입력" value={inputValue} onChange={(e) => setInputValue(e.target.value)}/>
            <button onClick={onClick}>입력</button>
            <div>입력 값 : {inputValue}</div>
            {
                list?.length && list.map((item) => <Item text={item}/> )
            }
        </main>
    )

}

// Item.jsx
import React from 'react'

export default function Item({text}) {
    return (
        <li>{text}</li>
    )
}
{%endhighlight%}

{%highlight Vue%}
// Vue

//Index.vue


<script>
import Item from "./Item";
export default {
  components: {Item},
  data: function () {
    return {
      name: 'Vue',
      inputValue: '',
      list: ['item1', 'item2']
    }
  },
  methods: {
    onClick() {
      this.list.push(this.inputValue);
    }
  }
}
</script>

<template>
  <main>
    <h1>Hello {{name}}</h1>
    <p>Visit the {{name}}!!.</p>
    <input type="text" placeholder="입력" v-model="inputValue">
    <button v-on:click="onClick">입력</button>
    <div>입력 값 : {{inputValue}}</div>

    <Item v-for="text in list" v-bind:text="text"/>

  </main>
</template>
{%endhighlight%}

{%highlight javascript%}
// Svelte

// Index.svelte
<script>
	import Item from "./Item.svelte";

	let name = 'Svelte';
	let inputValue = '';
	let list = ['item1', 'item2'];
	function onClick() {
		list = [...list, inputValue];
	}
</script>

<main>
	<h1>Hello {name}!</h1>
	<p>Visit the Svelte!!</p>
	<input type="text" placeholder="입력" bind:value={inputValue}>
	<button on:click={onClick}>입력</button>
	<div>입력 값 : {inputValue}</div>
	{#each list as text}
		<Item text={text}/>
	{/each}
</main>

// Item.svelte
<script>
    export let text;
</script>

<li>{text}</li>
{%endhighlight%}

## 5. Style 정의
- vue와 같이 template, script, style 형식의 포맷으로 한 파일을 구성합니다.
{%highlight javascript%}
// Svelte
<script>
	import Item from "./Item.svelte";

	let name = 'Svelte';
	let inputValue = '';
	let list = ['item1', 'item2'];
	function onClick() {
		list = [...list, inputValue];
	}
</script>

<main>
	<h1>Hello {name}!</h1>
	<p>Visit the Svelte!!</p>
	<input type="text" placeholder="입력" bind:value={inputValue}>
	<button on:click={onClick}>입력</button>
	<div>입력 값 : {inputValue}</div>
	{#each list as text}
		<Item text={text}/>
	{/each}
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
{%endhighlight%}용

## 6. 컴포넌트 정의 및 사용.
- 기존의 library or framework와 같이 jsx, vue와 같이 .svelte extensions를 가진 파일에 컴포넌트를 정의 후 export 합니다.
- 필요한 컴포넌트를 import 후 사용 및 props를 전달합니다.

{%highlight javascript%}
// Index.svelte
<script>
	import Item from "./Item.svelte";

	let name = 'Svelte';
	let inputValue = '';
	let list = ['item1', 'item2'];
	function onClick() {
		list = [...list, inputValue];
	}
</script>

<main>
	<h1>Hello {name}!</h1>
	<p>Visit the Svelte!!</p>
	<input type="text" placeholder="입력" bind:value={inputValue}>
	<button on:click={onClick}>입력</button>
	<div>입력 값 : {inputValue}</div>
	{#each list as text}
		<Item text={text}/>
	{/each}
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


// Item.svelte
<script>
    export let text;
</script>

<li>{text}</li>
{%endhighlight%}


## 7. Life Cycle
- life cycle 순서 :  beforeUpdate, onMount, afterUpdate, onDestroy
- 최초 컴포넌트가 마운트 되기전에 beforeUpdate가 먼저 실행이 됩니다.
- 컴포넌트에서 변경이 감지되면 beforeUpdate 발생 후 afterUpdate가 발생합니다.
<img src="https://media.vlpt.us/images/katanazero86/post/8f049c16-d6d4-4f5f-8f98-e3aa834345fb/Svelte%20life%20cycle.png"/>
<b style="font-size: 9px;">출처: https://velog.io/@katanazero86/svelte-life-cycle-%EC%83%9D%EB%AA%85%EC%A3%BC%EA%B8%B0</b>
- beforeUpdate, onMount, afterUpdate, onDestroy 각 life cycle을 아래 코드를 통해 확인할 수 있습니다. -> onDestroy의 경우 삭제 버튼 클릭 시 호출
{%highlight javascript%}
    //Index.svelte
    <script>
    	import LifeCycle from "./LifeCycle.svelte";
    
    	let name = 'Svelte';
    	let show = true;
    	function onClickRemoveParent() {
    		show=false;
    	}
    </script>
    
    <main>
    	<h1>Hello {name}!</h1>
    	<p>Visit the Svelte!!</p>
    
    	{#if show}
    	<LifeCycle/>
    	{/if}
    	<button on:click={onClickRemoveParent}>부모 노드 삭제</button>
    </main>
    
    // LifeCycle.svelte
    <script>
        import {onMount, onDestroy, beforeUpdate, afterUpdate, tick} from 'svelte'
    
        onMount(() => {
            console.log('onMount');
        });
    
        onDestroy(() => {
            console.log('onDestroy');
        });
    
        beforeUpdate(() => {
            console.log('beforeUpdate');
        });
    
        afterUpdate(() => {
            console.log('afterUpdate');
        });
    </script>
{%endhighlight%}
<img src="{{site.baseurl}}/images/lifeCycle.png"/>

- svelte에는 특이하게도 tick이라는 lifeCycle이 있습니다.
- tick은 다른 라이프사이클과 다르게 첫 초기화를 제외하면 언제나 호출할 수 있습니다.
- tick은 promise를 return하며 상태 변경이 dom에 적용될때 까지 pending됩니다.

{%highlight javascript%}
// LifeCycle.svelte
  <script>
      import {onMount, onDestroy, beforeUpdate, afterUpdate, tick} from 'svelte'
  
      onMount(() => {
          console.log('onMount');
      });
  
      onDestroy(() => {
          console.log('onDestroy');
      });
  
      beforeUpdate(() => {
          console.log('beforeUpdate');
      });
  
      afterUpdate(() => {
          console.log('afterUpdate');
      });
  
      beforeUpdate(async () => {
         console.log("beforeUpdate with tick");
         await tick();
         console.log("tick ....")
      });
  
  </script>
{%endhighlight%}
<img src="{{site.baseurl}}/images/withTick.png"/>

<pre class="source">
출처 :
 - https://2020.stateofjs.com/ko-KR/technologies/front-end-frameworks/
 - https://velog.io/@katanazero86/svelte-life-cycle-%EC%83%9D%EB%AA%85%EC%A3%BC%EA%B8%B0
git example :
 - https://github.com/JongHyuckLee/svelte-study
 
</pre>
