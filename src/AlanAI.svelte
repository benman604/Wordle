<script> 
    import alanBtn from "@alan-ai/alan-sdk-web";
    import { createEventDispatcher } from 'svelte';
	import Allwords from './words.js';
    const dispatch = createEventDispatcher();

    var alanBtnInstance = alanBtn({
        key: "7a742499c23cc5ae962949260e3edd9f2e956eca572e1d8b807a3e2338fdd0dc/stage",
        onCommand: function (data) {
            let x = data.word
            x = x.toLowerCase()
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

                
                alanBtnInstance.playText("That's a great guess!")

            }
            else{
                dispatch("message", {message: data.word + " is not a word!"})
            }
        },
        rootEl: document.getElementById("alan-btn"),
    });

</script>

<div class="alan-btn"></div>