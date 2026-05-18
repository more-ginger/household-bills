<script lang="ts">
	import FigureData from '$lib/data/figure.json';
	import { resolve } from '$app/paths';
	import { fade } from 'svelte/transition';

	let { step } = $props();
</script>

<div class="relative h-full w-full">
	{#key step}
		<div
			in:fade={{ duration: 400, delay: 150 }}
			out:fade={{ duration: 250 }}
			class={`absolute inset-0 align-center m-auto content-center justify-center ${FigureData['figure-steps'][step].title === 'population' ? 'grid w-[70vw] grid-cols-10 grid-rows-6 py-10' : 'flex w-6/7 flex-wrap'}`}
		>
			{#each FigureData['figure-steps'][step].urls as figure, f}
				<div><img class={`${FigureData['figure-steps'][step].css}`} src={resolve(figure)} alt="" /></div>
			{/each}
		</div>
	{/key}
</div>
