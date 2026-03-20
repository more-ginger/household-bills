<script lang="ts">
	import { scaleLinear, scaleBand } from 'd3-scale';
	import { max, min } from 'd3-array';
	let { selectedFactorData } = $props();

	let width = $state(0);
	let height = $state(0);
	let marginX = $derived(width / 10);

	const maxPositiveNumber = $derived(
		max(
			selectedFactorData.rel.map((d: { riskvaluetosplot_1dp: number }) => {
				return +d.riskvaluetosplot_1dp;
			})
		)
	);

	const minNegativeNumber = $derived(
		min(
			selectedFactorData.rel.map((d: { riskvaluetosplot_1dp: number }) => {
				return +d.riskvaluetosplot_1dp;
			})
		)
	);

	const xScale = $derived(
		scaleBand()
			.domain(selectedFactorData.rel.map((d: {}, i: number) => i))
			.range([marginX, width])
			.padding(1)
	);

	const yScale = $derived(
		scaleLinear().domain([minNegativeNumber, maxPositiveNumber]).range([0, 100]).nice()
	);

	const yTicks = $derived(width > 0 && height > 0 ? yScale.ticks() : []);

	const barShapesForChart = $derived(
		selectedFactorData.rel.map((d: { riskvaluetosplot_1dp: number }, i: number) => {
			return {
				y2: yScale(+d.riskvaluetosplot_1dp),
				x: xScale(i),
				isNegative: +d.riskvaluetosplot_1dp < 0
			};
		})
	);

	$inspect(yTicks);
</script>

<svg
	width="100%"
	class="h-[55vh] bg-green-600 md:h-full"
	bind:clientWidth={width}
	bind:clientHeight={height}
>
	<defs>
		<marker
			id="positive-arrowhead"
			markerWidth="10"
			markerHeight="10"
			refX="5"
			refY="5"
			orient="auto"
		>
			<path d="M 0 1 L 5 5 L 0 9" stroke="currentColor" fill="transparent" />
		</marker>
		<marker
			id="negative-arrowhead"
			markerWidth="10"
			markerHeight="10"
			refX="5"
			refY="5"
			orient="auto-start-reverse"
		>
			<path d="M 0 1 L 5 5 L 0 9" stroke="currentColor" fill="transparent" />
		</marker>
	</defs>
	{#if width > 0 && height > 0}
		<g class="axis">
			<line x1="0" y1={height / 2} x2={width} y2={height / 2} stroke="white" stroke-width="3" />
			<g>
				{#each yTicks as tick}
					<line
						x1={marginX}
						y1={yScale(tick)}
						x2={width}
						y2={yScale(tick)}
						stroke="#ccc"
						stroke-width="1"
					/>
					<text x={marginX} y={yScale(tick)}>{tick}</text>
				{/each}
			</g>
		</g>
	{/if}
	{#each barShapesForChart as bar}
		<line
			y2={bar.isNegative ? height / 2 : bar.y2}
			x2={bar.x}
			y1={bar.isNegative ? height / 2 + bar.y2 : height / 2}
			x1={bar.x}
			stroke={bar.isNegative ? 'red' : 'white'}
			stroke-width="3"
			marker-end={bar.isNegative ? '' : 'url(#positive-arrowhead)'}
			marker-start={bar.isNegative ? 'url(#negative-arrowhead)' : ''}
		/>
	{/each}
</svg>
