<script>
	import { onMount, createEventDispatcher } from 'svelte';
	import WordGrid from './WordGrid'
	import Keyboard from './Keyboard.svelte'
	let dispatch = createEventDispatcher()

	let word = ""
	let state = 0
	let tries = word.length
	let results = []

	window["keypress"] = []
	window["keycolor"] = []

	let message = ""
	function handleMessage(event){
		if(event.detail.message != undefined){
			message = event.detail.message
			setTimeout(() => {
				message = ""
			}, 1000)
			state--
		} else{
			let x = event.detail.state
			results.push(x)
			if(x == "correct"){
				state = "win"
			} 
			else if(x == "incorrect"){
				if(state + 1 >= tries){
					state = "lose"
				}
				else{
					state++
				}
			}
		}
	}

	function handleKeyclick(event){
		console.log(event.detail.key)
		return {key: event.detail.key}
	}

	function getState(i){
		if(i == state){
			return "typing"
		}
		if(i > state){
			return "waiting"
		}
		return results[i]
	}

	function randInt(min, max) {
		min = Math.ceil(min);
		max = Math.floor(max);
		return Math.floor(Math.random() * (max - min) + min)
	}

	onMount(() => {
		if(word == ""){
			fetch(`https://fly.wordfinderapi.com/api/search?length=${randInt(5,7)}&dictionary=wwf2&word_sorting=points&group_by_length=true&page_size=1&page_token=${randInt(0, 50)}`)
			.then(res => res.json())
			.then(data => {
				word = data.word_pages[0].word_list[0].word.toLowerCase()
				// console.log(word)
				tries = word.length
				state = 0
				results = []
			})
		}
	})
</script>

<main>
	<div class="message">
		<h1>WORDLE</h1> 
		<a href="https://github.com/benman604/Wordle">Github</a> 
		<a href="https://www.powerlanguage.co.uk/wordle/">Origional</a>
		<br><br>
		{#if message != ""}
			<strong>{message}</strong>
		{/if}
		{#if state == "win"}
			<strong>You won!</strong>
			<button on:click={()=>{window.location.reload()}}>New Game</button>
		{/if}

		{#if state == "lose"}
			<strong>You lost!</strong>
			<p>The word was {word}.</p>
			<button on:click={()=>{window.location.reload()}}>New Game</button>
		{/if}
	</div>
	<br>
	<table>
		{#each {length: tries} as _, i}
			<WordGrid state={getState(i, state)} correct={word} on:message={handleMessage}/>
		{/each}
	</table>

	<Keyboard on:keyclick={handleKeyclick} />
</main>

<style>
	main{
		margin: 0;
		position: absolute;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
	}

	.message *{
		margin-right: 10px;
		display: inline;
	}

	@media only screen and (max-width: 600px), (max-height: 700px){
		main{
			width: 100%;
			top: 50px;
			left: 50%;
			transform: translate(-50%, 0);
		}
		table{
			margin-left: auto;
			margin-right: auto;
		}
		.message{
			text-align: center;
		}
		.message *{
			display: block;
		}
	}
</style>
