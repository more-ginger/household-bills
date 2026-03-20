<script lang="ts">
	import { scaleLinear, scaleBand } from 'd3-scale';
	import { max } from 'd3-array';
	let { selectedFactorData } = $props();

	let width = $state(0);
	let height = $state(0);
	let marginX = $derived(width / 10);

	const maxPositiveNumber = $derived(
		max(
			selectedFactorData.rel.map((d: { final_howmuchcalc1dp: number }) => {
				return +d.final_howmuchcalc1dp;
			})
		)
	);

	const xScale = $derived(
		scaleBand()
			.domain(selectedFactorData.rel.map((d: {}, i: number) => i))
			.range([marginX, width])
			.padding(1)
	);

	const positiveYScale = $derived(
		scaleLinear().domain([0, maxPositiveNumber]).range([0, 100]).nice()
	);

	const barShapesForChart = $derived(
		selectedFactorData.rel.map((d: { final_howmuchcalc1dp: number }, i: number) => {
			return {
				y2: positiveYScale(+d.final_howmuchcalc1dp),
				x: xScale(i)
			};
		})
	);

	$inspect(selectedFactorData.rel);
</script>

<svg
	width="100%"
	class="h-[55vh] bg-green-600 md:h-full"
	bind:clientWidth={width}
	bind:clientHeight={height}
>
	<defs>
		<marker id="arrowhead" markerWidth="10" markerHeight="7" refX="10" refY="3.5" orient="auto">
			<polygon points="0 0, 10 3.5, 0 7" fill="currentColor" />
		</marker>
	</defs>
	<g class="axis">
		<line x1="0" y1={height / 2} x2={width} y2={height / 2} stroke="white" stroke-width="3" />
	</g>
	{#each barShapesForChart as bar}
		<line
			y2={bar.y2}
			x2={bar.x}
			y1={height / 2}
			x1={bar.x}
			stroke="white"
			stroke-width="3"
			marker-end="url(#arrowhead)"
		/>
	{/each}
</svg>
