<script lang="ts">
	import FigureData from '$lib/data/figure.json';

	let { step } = $props();
	$inspect('from figure', step);
</script>

{#if step === 0}
	<div class="align-center m-auto flex h-full w-4/7 flex-wrap content-center justify-items-center">
		{#each FigureData['figure-steps'][2].urls as figure}
			<div><img class="mx-2 h-20" src={figure} /></div>
		{/each}
	</div>
{/if}
