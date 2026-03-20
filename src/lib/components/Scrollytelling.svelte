<script lang="ts">
	import scrollama from 'scrollama';
	import Text from '../content/test.md?raw';
	import ScrollyBlocks from './scrollytelling/ScrollyBlocks.svelte';
	import ScrollyFigure from './scrollytelling/ScrollyFigure.svelte';

	const scroller = scrollama();
	let step: number = $state(0);
	let direction: 'up' | 'down' | null = $state(null);

	let textIsRendered: boolean = $state(false);
	let textHasSteps: boolean = $state(false);

	// the event is dispatched from BlockRenderer component if scrolly
	function onTextRender(data: { isTextRendered: boolean; hasSteps: boolean }) {
		textIsRendered = data.isTextRendered;
		textHasSteps = data.hasSteps;
	}

	$effect(() => {
		setTimeout(() => {
			scroller
				.setup({
					step: '.step',
					debug: false,
					offset: 0.7
				})
				.onStepEnter((response) => {
					step = response.index;
					direction = response.direction;

					console.log(step);
				})
				.onStepProgress((response) => {})
				.onStepExit((response) => {});
		}, 200);
	});
</script>

<div class="relative">
	<figure class="sticky top-0 h-dvh w-full">
		<ScrollyFigure {step} />
	</figure>
	<article class="relative"><ScrollyBlocks rawScrollyText={Text} {onTextRender} /></article>
</div>
