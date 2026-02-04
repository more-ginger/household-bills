<script lang="ts">
	import FigureData from '$lib/data/figure.json';

	let { step } = $props();
	$inspect('from figure', FigureData['figure-steps'][step]);
</script>

<div
	class="align-center m-auto flex h-full w-6/7 flex-wrap content-center justify-center justify-items-stretch"
>
	{#each FigureData['figure-steps'][step].urls as figure}
		<div><img class="max-h-100" src={figure} /></div>
	{/each}
</div>
