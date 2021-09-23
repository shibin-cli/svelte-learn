<script lang="ts">
    import {createEventDispatcher} from 'svelte'
    export let name: string = 'wd';
    const dispatch = createEventDispatcher()
    function sayHello(){
        dispatch('message',{
            text: 'hello'
        })
    }
</script>

<h1>Hello {name}!</h1>
<button on:click={sayHello}>Say hello</button>
<style>
    h1 {
        color: #ff3e00;
        text-transform: uppercase;
        font-size: 4em;
        font-weight: 100;
    }
</style>
