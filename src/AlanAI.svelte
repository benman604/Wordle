<script> 
    import alanBtn from "@alan-ai/alan-sdk-web";

    var alanBtnInstance = alanBtn({
        key: "7a742499c23cc5ae962949260e3edd9f2e956eca572e1d8b807a3e2338fdd0dc/stage",
        onCommand: function (data) {
            for(let i=0; i< data.word.length; i++){
                let key = (data.word[i])
                for(let fn of window["keypress"]){
                    fn({key: key})
                }
            }
            for(let fn of window["keypress"]){
                fn({key: "Enter"})
            }
        },
        rootEl: document.getElementById("alan-btn"),
    });

//         if(checkVoiceWord(word)){
    //             check()
    //             return
    //         }
    //         else{
    //             p.play('Your word input length is not 5. Please say a 5 letter word.');
    //         }
</script>

<div class="alan-btn"></div>