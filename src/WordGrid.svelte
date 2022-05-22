<script>
    // import word-stream
    // import random-word
    import {createEventDispatcher} from 'svelte'
	const dispatch = createEventDispatcher();
	import Allwords from './words.js'


    export let correct
    export let state
    let word = ""
    let color = "waiting,".repeat(correct.length).split(",")

    function getColor(i){
        if(i == word.length && state != "waiting"){
            return "cursor"
        }
        return color[i]
    }

    function getChar(i){
        if(i < word.length){
            return word[i]
        }
        return " "
    }

    function countChar(wd, char){
        return wd.split(char).length - 1
    }

    async function check(){
        // if(word != correct && !window["w"].includes(word)){
        //     dispatch("message", {message: "That's not a word!"})
        //     word = ""
        //     color = "waiting,".repeat(correct.length).split(",")
        //     return
        // }

        let tempCorrect = correct
        let usedG = {}
        for(let i = 0; i < word.length; i++){
            if(word[i] == correct[i]){
                color[i] = "green"
                tempCorrect = tempCorrect.replace(word[i], "")
                if(usedG[word[i]] == undefined){
                    usedG[word[i]] = 0
                }
                usedG[word[i]]++
            } else{
                color[i] = "black"
            }
        }

        let misplaced = {}
        for(let i = 0; i < word.length; i++){
            misplaced[word[i]] = countChar(tempCorrect, word[i])
        }

        for(let i = 0; i < word.length; i++){
            if(misplaced[word[i]] > 0 && color[i] != "green"){
                color[i] = "yellow"
                misplaced[word[i]]--
            }
        }
        
        let reformatedColors = {black: [], yellow: [], green: []}
        for(let i = 0; i < word.length; i++){
            reformatedColors[color[i]].push(word[i])
        }
        window["updateKeyboard"](reformatedColors)
        window["keycolor"].push(color)
        state = (word == correct) ? "correct" : "incorrect"
        setTimeout(() => {
            dispatch('message', {state: state})
        }, 100)
    }

    function keypress(event){
        if(state == "typing" && word.length <= correct.length){
            if(event.key == "Enter" && word.length == correct.length){
                if(Allwords.split("\n").includes(word)){
                    check()
                    return
                }
                dispatch("message", {message: word + " is not a word!"})
            }
            if(event.key == "Backspace"){
                word = word.substring(0, word.length - 1)
            } else if(event.key.length == 1 && word.length < correct.length){
                word += event.key.toLowerCase()
            }
        }
    }

    // function wordInput(){
    //     intent('$(Word)', p => {
    //         word = p.Word.value;
    //         if(checkVoiceWord(word)){
    //             check()
    //             return
    //         }
    //         else{
    //             p.play('Your word input length is not 5. Please say a 5 letter word.');
    //         }
    //     });
    // }
    // function checkVoiceWord(checkWord){
    //     if (checkWord.length == 5){
    //         return true;
    //     }
    //     return false;
    // }

    window["keypress"].push(keypress)
</script>

<svelte:window on:keydown={keypress}/>
<tr>
    {#each {length: correct.length} as _, i}
        <th class="{getColor(i, word, state)}">{getChar(i, word)}</th>
    {/each}
</tr>

<style>
    th{
        width: 75px;
        height: 75px;
        font-size: xx-large;
        color: white;
        text-transform: uppercase;
    }
</style>
