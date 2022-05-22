<script>
	import { onMount, createEventDispatcher } from 'svelte';
	import { scale } from 'svelte/transition';
	import WordGrid from './WordGrid'
	import Keyboard from './Keyboard.svelte'
	import Allwords from './words.js'
	import AlanAI from './AlanAI.svelte'


	let word = ""
	let state = 0
	let tries = word.length
	let results = []
	let shareResultsContent = "Svelte-AlanAI-Wordle "

	window["keypress"] = []
	window["keycolor"] = []

	let message = ""
	function handleMessage(event){
		if(event.detail.message != undefined){
			message = event.detail.message
			setTimeout(() => {
				message = ""
			}, 1000)
		} else{
			let x = event.detail.state
			results.push(x)
			if(x == "correct"){
				state = "win"
				buildResultContent()
			} 
			else if(x == "incorrect"){
				if(state + 1 >= tries){
					state = "lose"
					buildResultContent()
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

	function buildResultContent(){
		let score = (state == "win") ? (window["keycolor"].length) : "X"
		shareResultsContent += score + "/6 \n"
		for(let row of window["keycolor"]){
			for(let color of row){
				if(color == "black"){
					shareResultsContent += "⬛"
				}
				if(color == "green"){
					shareResultsContent += "🟩"
				}
				if(color == "yellow"){
					shareResultsContent += "🟨"
				}
			}
			shareResultsContent += "\n"
		}
	}

	let shareBtnContent = "Share"
	function copyResults(){
		navigator.clipboard.writeText(shareResultsContent).then(function() {
			shareBtnContent = ('Copied results');
		}, function(err) {
			shareBtnContent = ('Could not copy text: ', err);
		});

		setTimeout(() => {
			shareBtnContent = "Share"
		}, 2000);
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
			let aw = Allwords.split("\n")
			word = aw[randInt(0, aw.length)]
			//word = randomWords({maxLength: 7, minLength: 5, exactly: 1})[0]
			console.log(word)
			tries = word.length + 1
			state = 0
			results = []
		}
	})
</script>

<main>
	<div class="message">
		<h1>WORDLE</h1> 
		<a href="https://github.com/benman604/Wordle">Github</a> 
		<a href="https://www.powerlanguage.co.uk/wordle/">Original</a>
		<br><br>
		{#if message != ""}
			<strong transition:scale>{message}</strong>
		{:else}
			<p></p>
		{/if}
		{#if state == "win"}
			<div transition:scale>
				<strong>You won!</strong>
				<button on:click={copyResults}>{shareBtnContent}</button>
				<button on:click={()=>{window.location.reload()}}>New Game</button>
			</div>
		{/if}

		{#if state == "lose"}
			<div transition:scale>
				<strong>You lost!</strong>
				<p>The word was {word}.</p>
				<button transition:scale on:click={copyResults}>{shareBtnContent}</button>
				<button on:click={()=>{window.location.reload()}}>New Game</button>
			</div>
		{/if}
	</div>
	<br>
	<table>
		{#each {length: tries} as _, i}
			<WordGrid state={getState(i, state)} correct={word} on:message={handleMessage}/>
		{/each}
	</table>

	<Keyboard on:keyclick={handleKeyclick} />
	<AlanAI on:message={handleMessage} correct={word} bind:state={state}/>
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

	@media only screen and (max-width: 600px), (max-height: 900px){
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

		/* center buttons inside main */
		/* button{
			margin-left: auto;
			margin-right: auto;
			position: absolute;
		} */
	}
</style>
