<script lang="ts">
	import { group, extent, max } from 'd3-array';
	import { line, curveMonotoneX } from 'd3-shape';
	import { scaleLinear } from 'd3-scale';
	let { selectedFactorData, selectedFactor } = $props();

	let width = $state(0);
	let height = $state(0);
	let marginX = 40;

	let hoveredIndex = $state<number | null>(null);

	// Reset tooltip whenever the selected factor changes
	$effect(() => {
		selectedFactor;
		hoveredIndex = null;
	});

	const xDomain: [number, number] = $derived(
		extent(selectedFactorData.trend.map((d: { Year: number }) => d.Year))
	);

	const yMax: [number, number] = $derived(
		max(selectedFactorData.trend.map((d: { Percentage: number }) => d.Percentage))
	);

	const xScale = $derived(
		scaleLinear()
			.domain(xDomain)
			.range([marginX + 25, width - marginX])
	);

	const yScale = $derived(
		scaleLinear()
			.domain([0, yMax + 5])
			.range([height - 40, 30])
			.nice()
	);

	const yTicks = $derived(width > 0 && height > 0 ? yScale.ticks() : []);
	const xTickCount = $derived(width < 400 ? 3 : width < 600 ? 5 : 8);
	const xTicks = $derived(width > 0 && height > 0 ? xScale.ticks(xTickCount) : []);

	let groupedData = $derived(group(selectedFactorData.trend, (d: { Group: string }) => d.Group));

	const lineGenerator = $derived(
		line<{ Year: number; Percentage: number }>()
			.x((d) => xScale(d.Year))
			.y((d) => yScale(d.Percentage))
			.curve(curveMonotoneX)
	);

	const lineData = $derived(
		[...groupedData.entries()].map(([key, values]) => ({
			group: key,
			path: lineGenerator(values)
		}))
	);

	$inspect(lineData);

	// ── Validity guard ────────────────────────────────────────────────────────
	const hasValidData = $derived(
		selectedFactorData['has-trend'] ||
			(selectedFactorData.rel.length > 0 &&
				selectedFactorData.rel.some(
					(d: { riskvaluetosplot_1dp: number }) => !isNaN(+d.riskvaluetosplot_1dp)
				))
	);
</script>

{#if !hasValidData}
	<div class="flex h-full min-h-0 flex-1 items-center justify-center">
		<p class="text-white opacity-60">No data available for this risk factor.</p>
	</div>
{:else}
	<div class="relative h-full min-h-0 flex-1">
		<svg
			class="font-epilogue"
			width="100%"
			height="100%"
			bind:clientWidth={width}
			bind:clientHeight={height}
		>
			{#if width > 0 && height > 0}
				{#each lineData as line}
					<path d={line.path} stroke="white" fill="none" stroke-width="3" />
				{/each}
				{#each xTicks as xtick}
					<line
						x1={xScale(xtick)}
						x2={xScale(xtick)}
						y1={height - 35}
						y2={35}
						stroke="white"
					></line>
					<text x={xScale(xtick)} y={height - 10} fill="white" text-anchor="middle">{xtick}</text>
				{/each}
				{#each yTicks as ytick}
					<line x1={marginX} x2={width} y1={yScale(ytick) + 5} y2={yScale(ytick) + 5} stroke="white"
					></line>
					<text x={marginX} y={yScale(ytick)} fill="white">{ytick}</text>
				{/each}
				<!-- Chart content goes here -->
			{/if}
		</svg>

		<!-- Variable tooltip — shown on hover (desktop) or tap (mobile).
		     left/top to be wired to line chart data points -->
		{#if hoveredIndex !== null}
			<div
				class="pointer-events-none absolute z-10 max-w-[200px] rounded bg-white px-2 py-1 text-center font-epilogue text-xs text-accent-red shadow"
			>
				<!-- Tooltip content -->
			</div>
		{/if}
	</div>
{/if}
