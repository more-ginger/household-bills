<script lang="ts">
	import FigureData from '$lib/data/figure.json';

	let { step } = $props();
	//$inspect('from figure', FigureData['figure-steps'][step]);
</script>

<div
	class={`align-center m-auto  h-full w-6/7 content-center justify-center ${FigureData['figure-steps'][step].title === 'population' ? 'grid w-[70vw] grid-cols-10 grid-rows-6 py-10' : 'flex flex-wrap'}`}
>
	{#each FigureData['figure-steps'][step].urls as figure, f}
		<div><img class={`${FigureData['figure-steps'][step].css}`} src={figure} /></div>
	{/each}
</div>
