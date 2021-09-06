---
layout: post
title:  "[Svelte]Svelte State & Props"
date:   2021-09-06
tags:   Svelte.js State & Props
---
# Svelte.js State & Props
<pre class="info-panel">
 - 본 글은 props의 state와 props 정의와 사용 방법에 대해 알아보겠습니다.
 - React, Vue와 차이점을 중심으로 기술하겠습니다.
</pre>

## 1. 상태 정의
- 상태 정의는 VanillaJs와 같이 변수 선언을 통해 상태를 정의합니다.
- React와 Vue에 비해 보일러플레이트 코드가 적은것이 특징입니다.
- React는 state에 관해 setter를 제공하며 이 setter를 통해 데이터를 변경해야 합니다.(이를 통해 리렌더링)
- Vue는 data function 내부에 return 값이 state가 되며, React와 달리 이에 대한 직접 할당으로 데이터를 변경합니다.
- Svelte의 경우 <script></script> 내부에 변수 및 함수를 정의하며 이를 직접 할당하며 데이터를 변경합니다.
- Array, Object의 경우 주소값을 유지하며 내부 데이터를 조작할 경우 리렌더링이 되지 않습니다. 객체에 대한 재할당을 해야 Svelte가 이를 알아채고 화면을 다시 그립니다.
- state 할당 방식은 Vue와 비슷하게 동작하는 듯 합니다.

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

## 2. method or function 정의.
- 내부 function or method의 정의도 vanillaJs와 같이 선언형으로 합니다.
- React의 경우 함수 작성의 경우 스코프내 useCallback, useMemo를 이용하거나, 일반 함수를 선언해 사용합니다.
- Vue의 경우 methods 내부에 함수를 정의해 사용합니다.
- Svelte의 경우 <script></script> 태그내에 함수를 선언하고 사용합니다.

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

## 3. Props 전달 방식.
- 기존의 방식과 비슷하지만 간단 명료한 정의와 전달 방식을 갖고 있습니다.
- 컴포넌트에 key={value} 형식으로 전달.
- 자식 컴포넌에서는 export keyword를 통해 props임을 명명.


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

## 4. function props 전달
- React : 변수 props와 마찬가지로 key=value 형식으로 전달
- Vue: 변수 props와 마찬가지로 kye=value 형식으로 전달할 수도 있고, @eventName 형식으로 전달해 child component에서 $emit을 통해 호출할 수 있습니다.
- Svelte: 변수 props와 마찬가지로 kye=value 형식으로 전달할 수도 있고, on:eventName 형식으로 전달해 child component에서 createEventDispatcher를 통해 dispatch할 수 있습니다.
{%highlight react%}
//React

//Index.jsx
import React from 'react'
import FunctionPropItem from "./FunctionPropItem";
export default function Index() {
    const [name] = React.useState('React');
    
    const onAlert = (text, e) => {
        alert(`parameter : ${text}, event target value : ${e.target.value}`);
    }

    return (
        <main>
            <h1 style={{color: "#00D8FF"}}>Hello {name}</h1>
            <p>Visit the {name}</p>
            <FunctionPropItem onAlert={onAlert}/>
        </main>
    )

}

//FunctionPropItem.jsx
import React from 'react'

export default function AlertPropItem ({onAlert}) {
    return (
        <button value="React" onClick={(e) => onAlert('React Alert', e)}>alert 호출</button>
    )
}
{%endhighlight%}
{%highlight vue%}
//Vue

//Index.vue
<script>
import FunctionPropItem from "./FunctionPropItem";
export default {
  components: {FunctionPropItem},
  data: function () {
    return {
      name: 'Vue',
    }
  },
  methods: {
    onAlert(text, e) {
      alert(`parameter : ${text}, event target value : ${e.target.value}`);
    },
    onAlert2(text, e) {
      alert(`parameter : ${text}, event target value : ${e.target.value}`);
    }
  }
}
</script>

<template>
  <main>
    <h1>Hello {{name}}</h1>
<!--    <p @click="()=>console.log('click Vue')">Visit the {{name}}!!.</p> Vue instance 외부 global에 접근할 수 없음.-->
    <p>Visit the {{name}}!!.</p>
    <FunctionPropItem @onAlert="onAlert" v-bind:onAlert2="onAlert2"/>
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

//FunctionPropItem.vue
<template>
  <div>
    <button value="Vue" @click="$emit('onAlert', 'Vue Alert', $event)">alert 호출하기</button>
    <button value="Vue2" @click="(e)=>onAlert2('Vue2', e)">alert 호출하기2</button>
<!--    //<div @click="(e) => alert('aa')">text</div> vm에 alert가 없으므로 alert가 동작하지 않음.-->
  </div>
</template>

<script>
export default {
  name: "FunctionPropItem",
  props: {
    onAlert2: Function
  },

}
</script>

{%endhighlight%}
{%highlight javascript%}
//Svelte

//Index.svelte
<script>
	import FunctionPropItem from "./FunctionPropItem.svelte";

	let name = 'Svelte';

	function onAlert(text, e) {
		alert(`parameter : ${text}, event target value : ${e.target.value}`);
	}

	function onAlert2(e) {
		alert(`parameter : ${e.detail.text}, event target value : ${e.detail.value}`);
	}
</script>

<main>
	<h1>Hello {name}!</h1>
	<p on:click={() => console.log("click Svelte")}>Visit the Svelte!!</p>
	<FunctionPropItem {onAlert} on:onAlert2={onAlert2}/>
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

//FunctionPropItem.svelte

<script>
    import {createEventDispatcher} from 'svelte'
    const dispatch = createEventDispatcher();
    export let onAlert

    function innerOnAlert () {
        dispatch('onAlert2', {
            text: 'Svelte2',
            value: 'Svelte Emit'
        })
    }
</script>

<button value="Svelte" on:click={(e) => onAlert('Svelte', e)}>alert 호출</button>
<button value="Svelte2" on:click={innerOnAlert}>alert 호출 2</button>
<button on:click={(e) => console.log("inner instance test")}>inner instance test</button>
<button on:click="{(e) => console.log('inner instance test2')}">inner instance test2</button>
{%endhighlight%}



<pre class="source">
git example :
 - https://github.com/JongHyuckLee/svelte-study
 
</pre>