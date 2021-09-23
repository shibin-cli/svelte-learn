<script lang="ts">
	import { onMount, onDestroy, beforeUpdate, afterUpdate } from "svelte";
	let canvas;
	let timer;
	onMount(() => {
		console.log(canvas);
		timer = setInterval(() => {
			console.log(new Date());
		}, 2000);
	});
	onDestroy(() => {
		clearInterval(timer);
	});
	beforeUpdate(() => {
		console.log("before");
	});
	afterUpdate(() => {
		console.log("afer");
	});
</script>

<canvas bind:this={canvas} />
