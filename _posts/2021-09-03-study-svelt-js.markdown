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
</pre>

## Svelte.js의 장점
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


<pre class="source">
출처 :
 - https://2020.stateofjs.com/ko-KR/technologies/front-end-frameworks/
 
git example:https://github.com/JongHyuckLee/svelte-study
 
</pre>
