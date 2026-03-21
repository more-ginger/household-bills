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

	$inspect(maxPositiveNumber);

	const absMax = $derived(
		Math.max(Math.abs(maxPositiveNumber ?? 0), Math.abs(minNegativeNumber ?? 0))
	);

	const xScale = $derived(
		scaleBand()
			.domain(selectedFactorData.rel.map((d: {}, i: number) => i))
			.range([marginX, width - marginX * 2])
			.padding(1)
	);

	const yScale = $derived(
		scaleLinear()
			.domain([-absMax, absMax])
			.range([height - 20, 20])
			.nice()
	);

	const yTicks = $derived(width > 0 && height > 0 ? yScale.ticks() : []);

	const barShapesForChart = $derived(
		selectedFactorData.rel.map((d: { riskvaluetosplot_1dp: number }, i: number) => {
			return {
				y2: yScale(+d.riskvaluetosplot_1dp),
				x: xScale(i)! + xScale.bandwidth() / 2,
				value: +d.riskvaluetosplot_1dp,
				isNegative: +d.riskvaluetosplot_1dp < 0
			};
		})
	);

	const baseline = $derived(yScale(0));
</script>

<svg width="100%" class="h-[55vh] md:h-full" bind:clientWidth={width} bind:clientHeight={height}>
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
		<marker id="zero-arrowhead" markerWidth="10" markerHeight="10" refX="5" refY="5" orient="auto">
			<circle cx="5" cy="5" r="2" fill="currentColor" />
		</marker>
	</defs>
	{#if width > 0 && height > 0}
		<g class="axis">
			<g>
				{#each yTicks as tick, t}
					<line
						x1={marginX + 10}
						y1={yScale(tick)}
						x2={width}
						y2={yScale(tick)}
						stroke="white"
						stroke-width="1"
						stroke-dasharray="1 2"
					/>
					<text x={marginX} y={yScale(tick) + 4} text-anchor="end" fill="white">{tick}</text>
					{#if t === 0}
						<text x={width} y={yScale(tick) - 10} text-anchor="end" fill="white"
							>Lower Risk (%)</text
						>
					{:else if t === yTicks.length - 1}
						<text x={width} y={yScale(tick) + 20} text-anchor="end" fill="white"
							>Higher Risk (%)</text
						>
					{/if}
				{/each}
			</g>
		</g>
		<line
			x1={marginX + 10}
			y1={baseline}
			x2={width}
			y2={baseline}
			stroke="white"
			stroke-width="3"
		/>
	{/if}
	{#each barShapesForChart as bar}
		<line
			y2={bar.y2}
			x2={bar.x}
			y1={baseline}
			x1={bar.x}
			stroke="white"
			stroke-width="3"
			marker-end={bar.value !== 0 ? 'url(#positive-arrowhead)' : 'url(#zero-arrowhead)'}
		/>
		<text x={bar.x} y={bar.isNegative ? bar.y2 + 20 : bar.y2 - 10} text-anchor="middle"
			>{bar.value}</text
		>
	{/each}
</svg>
