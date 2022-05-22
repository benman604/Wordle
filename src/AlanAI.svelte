<script> 
    import alanBtn from "@alan-ai/alan-sdk-web";
    import { createEventDispatcher } from 'svelte';
	import Allwords from './words.js';
    const dispatch = createEventDispatcher();
    export let correct;
    export let state;

    var alanBtnInstance = alanBtn({
        key: "7a742499c23cc5ae962949260e3edd9f2e956eca572e1d8b807a3e2338fdd0dc/stage",
        onCommand: function (data) {
            if(data.error != undefined){
                dispatch("message", {message: data.error})
                return
            }
            
            let x = data.word
            x = x.toLowerCase()
            x = x.replace(/\s/g, "")
            if(Allwords.split("\n").includes(x)){
                for(let i=0; i< data.word.length; i++){
                    let key = (data.word[i])
                    for(let fn of window["keypress"]){
                        fn({key: key})
                    }
                }

                for(let fn of window["keypress"]){
                    fn({key: "Enter"})
                }

                if(data.word == correct){
                    alanBtnInstance.playText("Congrats! You got it!")
                } else if(state >= 5){
                    alanBtnInstance.playText("You lost! The word was " + correct)
                } else{
                    alanBtnInstance.playText("Nice guess. ")
                }

            }
            else{
                dispatch("message", {message: data.word + " is not a word!"})
                alanBtnInstance.playText(data.word + "'s not a word!")
            }
        },
        rootEl: document.getElementById("alan-btn"),
    });

</script>

<div class="alan-btn"></div>