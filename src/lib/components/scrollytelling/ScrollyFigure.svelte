<script lang="ts">
	import FigureData from '$lib/data/figure.json';
	import { resolve } from '$app/paths';
	import { fade } from 'svelte/transition';

	let { step } = $props();
	$inspect(FigureData['figure-steps'][step]['has-footnote']);
</script>

<div class="relative h-full w-full">
	{#key step}
		<div
			in:fade={{ duration: 400, delay: 150 }}
			out:fade={{ duration: 250 }}
			class={`align-center absolute inset-0 m-auto content-center justify-center ${FigureData['figure-steps'][step].title === 'population' ? 'grid w-[70vw] grid-cols-10 grid-rows-6 py-10' : 'flex w-6/7 flex-wrap'}`}
		>
			{#each FigureData['figure-steps'][step].urls as figure, f}
				<div>
					<img class={`${FigureData['figure-steps'][step].css}`} src={resolve(figure)} alt="" />
				</div>
			{/each}
			{#if FigureData['figure-steps'][step]['has-footnote']}
				<div class="absolute right-0 bottom-10 flex w-2/6 items-center justify-end text-xs italic">
					<img
						class={`${FigureData['figure-steps'][step].title === 'population' ? 'order-1 h-[4em] pr-4' : 'order-2 h-[1.5em] pl-4'} w-auto`}
						src={resolve(FigureData['figure-steps'][step].footnote.img)}
						alt=""
					/>
					<div
						class={`${FigureData['figure-steps'][step].title === 'population' ? 'order-2 text-left' : 'order-1 text-right'}`}
					>
						{FigureData['figure-steps'][step].footnote.text}
					</div>
				</div>
			{/if}
		</div>
	{/key}
</div>
