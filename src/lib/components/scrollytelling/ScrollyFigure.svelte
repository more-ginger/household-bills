<script lang="ts">
	import FigureData from '$lib/data/figure.json';
	import { resolve } from '$app/paths';

	let { step } = $props();

	let width = $state(0);
	const isMobile = $derived(width > 0 && width < 768);

	let shownStep = $state(step);
	let visible = $state(true);

	$effect(() => {
		// React to step changes: fade out, swap content, fade in
		if (step === shownStep) return;
		visible = false;
		setTimeout(() => {
			shownStep = step;
			visible = true;
		}, 220);
	});

	function figureUrl(figureStep: (typeof FigureData)['figure-steps'][number], url: string): string {
		if (figureStep.title === 'chart' && isMobile) {
			return url.replace('chart.svg', 'chart-mobile.svg');
		}
		return url;
	}
</script>

<div class="relative h-full w-full overflow-hidden" bind:clientWidth={width}>
	<div
		class={`absolute inset-0 flex items-center justify-center overflow-hidden transition-opacity duration-200 ${FigureData['figure-steps'][shownStep].title === 'population' ? 'p-4 md:p-10' : 'p-4'}`}
		style="opacity: {visible ? 1 : 0};"
	>
		{#if FigureData['figure-steps'][shownStep].title === 'population' || FigureData['figure-steps'][shownStep].title === 'population-end'}
			<!-- Grid of human figures — 5 cols on mobile, 10 on desktop -->
			<div class="grid max-w-[95vw] grid-cols-8 md:w-[50vw] md:grid-cols-11 md:grid-rows-6">
				{#each FigureData['figure-steps'][shownStep].urls as figure}
					<div class="flex items-center justify-center">
						<img class="h-auto w-full object-contain" src={resolve(figure)} alt="" />
					</div>
				{/each}
			</div>
		{:else}
			<div class="flex flex-wrap justify-center">
				{#each FigureData['figure-steps'][shownStep].urls as figure}
					<img
						class={`${FigureData['figure-steps'][shownStep].css} w-auto object-contain`}
						src={resolve(figureUrl(FigureData['figure-steps'][shownStep], figure))}
						alt=""
					/>
				{/each}
			</div>
		{/if}
		{#if FigureData['figure-steps'][shownStep]['has-footnote']}
			<div
				class="absolute bottom-10 flex w-5/6 items-center justify-end bg-primary-blue/50 text-xs italic md:right-10 md:w-2/6 md:bg-transparent"
			>
				<img
					class={`${FigureData['figure-steps'][shownStep].title === 'population' ? 'h-[2em] md:h-[4em] ' : 'h-[2em] md:h-[1.5em]'} mr-6 w-auto`}
					src={resolve(FigureData['figure-steps'][shownStep].footnote.img)}
					alt=""
				/>
				<div>
					{FigureData['figure-steps'][shownStep].footnote.text}
				</div>
			</div>
		{/if}
	</div>
</div>
