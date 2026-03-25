<script lang="ts">
	import { scaleLinear, scaleBand } from 'd3-scale';
	import { max, min } from 'd3-array';
	let { selectedFactorData, selectedFactor } = $props();

	let width = $state(0);
	let height = $state(0);
	let marginX = $derived(width / 10);

	export const DEFAULT_FACTOR_ICON = 'human1-darkblue.webp';

	export const FACTOR_ICONS: Record<string, string> = {
		age: 'human9-darkblue.webp',
		carer: 'human8-darkblue.webp',
		disability: 'human12-darkblue.webp',
		'disability-related benefit': 'human12-darkblue.webp',
		'job-status': 'human2-darkblue.webp',
		'marital-status': 'human10-darkblue.webp',
		'number-inhousehold': 'human10-darkblue.webp',
		'number-of-dependent-children': 'human4-darkblue.webp'
	};

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

	const absMax = $derived(
		Math.max(Math.abs(maxPositiveNumber ?? 0), Math.abs(minNegativeNumber ?? 0))
	);

	const xScale = $derived(
		scaleBand()
			.domain(selectedFactorData.rel.map((d: {}, i: number) => i))
			.range([marginX, width - marginX * 2])
			.padding(0.4)
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
			const val = +d.riskvaluetosplot_1dp;
			const y2 = yScale(val);
			const rawTextY = val < 0 ? y2 + 20 : y2 - 10;
			return {
				y2,
				x: xScale(i)! + xScale.bandwidth() / 2,
				value: val,
				isNegative: val < 0,
				textY: Math.max(25, Math.min(rawTextY, height - 5))
			};
		})
	);

	const baseline = $derived(yScale(0));

	const hasValidData = $derived(
		selectedFactorData.rel.length > 0 &&
			selectedFactorData.rel.some(
				(d: { riskvaluetosplot_1dp: number }) => !isNaN(+d.riskvaluetosplot_1dp)
			)
	);

	$inspect(selectedFactorData);
</script>

{#if !hasValidData}
	<div class="flex h-[55vh] items-center justify-center md:h-full">
		<p class="text-white opacity-60">No data available for this risk factor.</p>
	</div>
{:else}
	<svg width="100%" class="h-[55vh] md:h-full" bind:clientWidth={width} bind:clientHeight={height}>
		<defs>
			<marker
				id="positive-arrowhead"
				markerWidth="5"
				markerHeight="5"
				refX="2.5"
				refY="2.5"
				orient="auto"
			>
				<path d="M 0 0 L 2.5 2.5 L 0 5" stroke="currentColor" fill="transparent" />
			</marker>
			<marker
				id="zero-arrowhead"
				markerWidth="10"
				markerHeight="10"
				refX="5"
				refY="5"
				orient="auto"
			>
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
							<text x={width} y={yScale(tick) - 10} text-anchor="end" fill="white" font-size="10"
								>Lower Risk (%)</text
							>
						{:else if t === yTicks.length - 1}
							<text x={width} y={yScale(tick) + 20} text-anchor="end" fill="white" font-size="10"
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
			<text x={width} y={baseline + 15} text-anchor="end" fill="white" font-size="10"
				>No meaningful difference from benchmark group (%)</text
			>
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
			<text x={bar.x} y={bar.textY} text-anchor="middle">{bar.value}</text>
		{/each}
		<image
			href={`illustrations/webp/humans/${FACTOR_ICONS[selectedFactor] ?? DEFAULT_FACTOR_ICON}`}
			height="70"
			width="70"
			x={width - 70}
			y={baseline - 70}
		/>
	</svg>
{/if}
