<script>
    import { scale } from 'svelte/transition'
    export let state = false
    function toggleState(){
        state = !state
    }
</script>

{#if state}
    <main transition:scale>
        <div class="content">
            <a class="right" href="#/" role="button" on:click|preventDefault={toggleState}>Close</a>
            <br>
            <slot></slot>
        </div>
    </main>
{/if}

<style>
    main {
        position: fixed;
        z-index: 1;
        left: 0;
        top: 0;
        width: 100%;
        height: 100%;
        overflow: auto;
    }

    .content {
        background-color: #ffffff;
        margin: 15% auto;
        padding: 20px;
        padding-top: 10px;
        width: 80%;
        box-shadow: 0 0 20px 0 rgba(0, 0, 0, 0.2);
    }

    .right {
        float: right;
    }
</style>