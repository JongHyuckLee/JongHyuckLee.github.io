---
layout: post
title:  "[Svelte]Svelte Life Cycle"
date:   2021-09-07
tags:   Svelte.js Life Cycle
---
# Svelte.js Life Cycle
<pre class="info-panel">
 - 본 글은 Svelte에서 라이프사이클에 대해 기술하겠습니다.
 - React, Vue와 차이점을 중심으로 기술하겠습니다.
</pre>

# Life Cycle
- life cycle 순서 :  beforeUpdate, onMount, afterUpdate, onDestroy
- 최초 컴포넌트가 마운트 되기전에 beforeUpdate가 먼저 실행이 됩니다.
- 컴포넌트에서 변경이 감지되면 beforeUpdate 발생 후 afterUpdate가 발생합니다.

- beforeUpdate, onMount, afterUpdate, onDestroy 각 life cycle을 아래 코드를 통해 확인할 수 있습니다. -> onDestroy의 경우 삭제 버튼 클릭 시 호출
{%highlight react%}
//React

//Index.jsx
import React from 'react'
import LifeCycle from "./LifeCycle";
export default function Index() {
    const [show, setShow] = React.useState(true);
    return (
        <main>
            {show && <LifeCycle/>}
            <button onClick={() => setShow(false)}>라이프 사이클 컴포넌트 삭제</button>
        </main>
    )

}

//LifeCycle.jsx
import React from 'react'

export default function LifeCycle() {
    const [doUpdate, setDoUpdate] = React.useState('라이프 사이클');
    React.useEffect(() => {
        console.log("모든 업데이트마다 실행");
        //componentDidMount, componentDidUpdate 수행
    });

    React.useEffect(() => {
        console.log("첫 번째 렌더링 시에만 실행");
    }, []);

    React.useEffect(() => {
        console.log("doUpdate update시에 실행");
    }, [doUpdate]);

    React.useEffect(() => {
        return () => console.log("unmount 시에 실행")
    }, [])

    return (
        <div>{doUpdate}</div>
    )
}
{%endhighlight%}
<img src="https://www.paduckk-dev.com/static/cb0ac82f8c5ab0ff4aff6a5818a6a92a/2c082/react_lifecycle_ko.jpg"/>
<b style="font-size: 9px;">출처: https://github.com/wojtekmaj/react-lifecycle-methods-diagram</b>
<img src="{{site.baseurl}}/images/lifeCycleReact.png"/>

{%highlight vue%}
//Vue

//Index.vue
<script>
import LifeCycle from "./LifeCycle";
export default {
  components: {LifeCycle},
  data: function () {
    return {
      show: true
    }
  },
  methods: {
    removeLifeCycle() {
      this.show = false;
    }
  }
}
</script>

<template>
  <main>
    <LifeCycle v-if="show"/>
    <button  @click="removeLifeCycle">라이프사이클 컴포넌트 삭제</button>
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

//LifeCycle.vue
<template>
  <div>라이프 싸이클</div>
</template>

<script>
export default {
  name: "LifeCycle",
  beforeCreate() {
    console.log("before Create");
  },
  created() {
    console.log("create");
  },
  beforeMount() {
    console.log("before Mount");
  },
  mounted() {
    console.log("mounted");
  },
  beforeUpdate() {
    console.log("before Update");
  },
  updated() {
    console.log("updated");
  },
  beforeDestroy() {
    console.log("before Destroy");
  },
  destroyed() {
    console.log("destroy");
  }
}
</script>
{%endhighlight%}
<img src="https://kr.vuejs.org/images/lifecycle.png"/>
<b style="font-size: 9px;">출처: https://kr.vuejs.org/v2/guide/instance.html#%EB%9D%BC%EC%9D%B4%ED%94%84%EC%82%AC%EC%9D%B4%ED%81%B4-%EB%8B%A4%EC%9D%B4%EC%96%B4%EA%B7%B8%EB%9E%A8</b>
<img src="{{site.baseurl}}/images/lifeCycleVue.png"/>


{%highlight javascript%}
 //Svelt
 
 //Index.svelte
 <script>
 	import LifeCycle from "./LifeCycle.svelte";
 
 	let name = 'Svelte';
 	let show = true;
 
 	function onClickRemoveParent() {
 		show = false;
 	}
 </script>
 
 <main>
 	<h1>Hello {name}!</h1>
 	{#if show}
 	<LifeCycle/>
 	{/if}
 	<button on:click={onClickRemoveParent}>LifeCycle 컴포넌트 삭제</button>
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
 <div>라이프 사이클</div>
{%endhighlight%}
<img src="https://media.vlpt.us/images/katanazero86/post/8f049c16-d6d4-4f5f-8f98-e3aa834345fb/Svelte%20life%20cycle.png"/>
<b style="font-size: 9px;">출처: https://velog.io/@katanazero86/svelte-life-cycle-%EC%83%9D%EB%AA%85%EC%A3%BC%EA%B8%B0</b>
<img src="{{site.baseurl}}/images/lifeCycleSvelte.png"/>

# Tick Life Cycle
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
출처:
- https://velog.io/@katanazero86/svelte-life-cycle-%EC%83%9D%EB%AA%85%EC%A3%BC%EA%B8%B0 
- https://www.paduckk-dev.com/static/cb0ac82f8c5ab0ff4aff6a5818a6a92a/2c082/react_lifecycle_ko.jpg
- https://github.com/wojtekmaj/react-lifecycle-methods-diagram
- https://kr.vuejs.org/v2/guide/instance.html#%EB%9D%BC%EC%9D%B4%ED%94%84%EC%82%AC%EC%9D%B4%ED%81%B4-%EB%8B%A4%EC%9D%B4%EC%96%B4%EA%B7%B8%EB%9E%A8
git example :
 - https://github.com/JongHyuckLee/svelte-study
 
</pre>