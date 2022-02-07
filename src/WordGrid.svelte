<script>
    
    import {createEventDispatcher} from 'svelte'
	const dispatch = createEventDispatcher()

    export let correct
    export let state
    let word = ""
    let color = "waiting,".repeat(correct.length).split(",")

    function getColor(i){
        if(i == word.length && state != "waiting"){
            return "cursor"
        }
        return color[i]
        // switch (color[i]) {
        //     case "waiting":
        //         return "background-color: rgb(240, 240, 240); color: black;"
        //     case "green":
        //         return "background-color: green; color: white;"
        //     case "yellow":
        //         return "background-color: yellow; color: black;"
        //     case "black":
        //         return "background-color: black; color: white;"
        //     default:
        //         return "background-color: rgb(240, 240, 240); color: black;"
        // }
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
        // if(word != correct){
        //     await fetch("https://api.dictionaryapi.dev/api/v2/entries/en/" + word)
        //     .then(res => res.json())
        //     .then(data => {
        //         if(data.title == "No Definitions Found"){
        //             dispatch("message", {message: "That's not a word!"})
        //             word = ""
        //             color = "waiting,".repeat(correct.length).split(",")
        //             return
        //         }
        //     })
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

        // misplaced = number of matches without greens in correct
        // find matches between word and tempcorrect
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
        state = (word == correct) ? "correct" : "incorrect"
        setTimeout(() => {
            dispatch('message', {state: state})
        }, 100)
    }

    function keypress(event){
        if(state == "typing" && word.length <= correct.length){
            if(event.key == "Enter" && word.length == correct.length){
                check()
                return
            }
            if(event.key == "Backspace"){
                word = word.substring(0, word.length - 1)
            } else if(event.key.length == 1 && word.length < correct.length){
                word += event.key.toLowerCase()
            }
        }
    }

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