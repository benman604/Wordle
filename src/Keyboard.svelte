<script>
    import {createEventDispatcher} from 'svelte'
	const dispatch = createEventDispatcher()

    let keys = [
        "qwertyuiop",
        "asdfghjkl",
        "←zxcvbnm→"
    ]

    function keyClick(key){
        if(key == "←"){
            key = "Backspace"
        }
        if(key == "→"){
            key = "Enter"
        }
        for(let fn of window["keypress"]){
            fn({key: key})
        }
    }

    let keycolors = {}
    function updateKeyboard(colors){
        for(let i of Object.keys(colors)){
            for(let j of colors[i]){
                keycolors[j] = i
            }
        }
        keycolors = keycolors
        console.log(keycolors)
    }

    window["updateKeyboard"] = updateKeyboard
</script>

<main>
    <br>
    {#each keys as key}
        <div class="row">
            {#each key as letter}
                <button class="{keycolors[letter]}" on:click={()=>{keyClick(letter)}}>{letter}</button>
            {/each}
        </div>
    {/each}
</main>

<style>
    .row{
        width: 100%;
        display: flex;
        align-items: center;
        justify-content: center;
    }
    .row button{
        margin-left: 2px;
        margin-right: 2px;
        width: 35px;
        height: 35px;
        text-transform: uppercase;
    }
</style>