---
layout: post
title:  "[Svelte]Svelte Component"
date:   2021-09-04
tags:   Svelte.js Component
---
# Svelte.js Component
<pre class="info-panel">
 - 본 글은 Svelte에서 Component 정의 및 사용법에 대해 기술하겠습니다.
 - React, Vue와 차이점을 중심으로 기술하겠습니다.
</pre>


# 컴포넌트 정의 및 사용.
- 기존의 library or framework와 같이 jsx, vue와 같이 .svelte extensions를 가진 파일에 컴포넌트를 정의 후 export 합니다.
- 필요한 컴포넌트를 import 후 사용 및 props를 전달합니다.
- React와 사용 방법이 흡사합니다. Component를 import 후 원하는 위치에 component를 사용하면 됩니다.
- Vue의 경우 Vue instance 내부에 component를 등록 후 사용해 주어야 합니다.
{%highlight react%}
//React

//Index.jsx
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
{%highlight vue%}
//Index.vue
<script>
//Vue

//Index.vue
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
<!--    <p @click="()=>console.log('click Vue')">Visit the {{name}}!!.</p> Vue instance 외부 global에 접근할 수 없음.-->
    <p>Visit the {{name}}!!.</p>
    <input type="text" placeholder="입력" v-model="inputValue">
    <button v-on:click="onClick">입력</button>
    <div>입력 값 : {{inputValue}}</div>

    <Item v-for="text in list" v-bind:text="text"/>
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

// Item.vue
<template>
  <li>{{text}}</li>
</template>

<script>
export default {
  name: "Item.vue",
  props: ['text']
}
</script>

{%endhighlight%}
{%highlight javascript%}
Svelte

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

<pre class="source">
git example :
 - https://github.com/JongHyuckLee/svelte-study
 
</pre>