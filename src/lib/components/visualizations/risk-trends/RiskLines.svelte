<script lang="ts">
	import { group, extent, max } from 'd3-array';
	import { line, curveMonotoneX } from 'd3-shape';
	import { scaleLinear } from 'd3-scale';
	let { selectedFactorData, selectedFactor } = $props();

	let width = $state(0);
	let height = $state(0);
	let marginX = 40;

	let hoveredIndex = $state<number | null>(null);
	let hoveredGroup = $state<string | null>(null);

	// Reset tooltip whenever the selected factor changes
	$effect(() => {
		selectedFactor;
		hoveredIndex = null;
		hoveredGroup = null;
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
		[...groupedData.entries()].map(([key, values]) => {
			const sorted = [...values].sort(
				(a: { Year: number }, b: { Year: number }) => a.Year - b.Year
			);
			const first = sorted[0];
			const last = sorted[sorted.length - 1];
			const increasing = sorted.length > 1 && last.Percentage > first.Percentage;
			const change = sorted.length > 1 ? +(last.Percentage - first.Percentage).toFixed(1) : 0;
			const changeLabel = (change >= 0 ? '+' : '') + change + '%';
			return {
				group: key,
				path: lineGenerator(values),
				increasing,
				gradientId: `line-gradient-${key.replace(/[^a-zA-Z0-9]/g, '-')}`,
				lastX: xScale(last.Year),
				lastY: yScale(last.Percentage),
				firstY: yScale(first.Percentage),
				changeLabel
			};
		})
	);

	const groupLabels = $derived(
		(() => {
			const items = lineData.map((l) => ({
				group: l.group,
				x: xScale(xDomain[0]) + 8,
				y: l.firstY
			}));
			const sorted = [...items].sort((a, b) => a.y - b.y);
			const resolved: { group: string; x: number; y: number }[] = [];
			for (const item of sorted) {
				const prev = resolved[resolved.length - 1];
				resolved.push({ ...item, y: prev ? Math.max(item.y, prev.y + 16) : item.y });
			}
			return resolved;
		})()
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
				<defs>
					{#each lineData as line}
						<linearGradient
							id={line.gradientId}
							gradientUnits="userSpaceOnUse"
							x1={xScale(xDomain[0])}
							y1="0"
							x2={xScale(xDomain[1])}
							y2="0"
						>
							<stop offset="0%" stop-color="white" />
							<stop offset="100%" stop-color={line.increasing ? '#FF5555' : 'white'} />
						</linearGradient>
					{/each}
				</defs>
				{#each lineData as line}
					{@const dimmed = hoveredGroup !== null && hoveredGroup !== line.group}
					<g
						opacity={dimmed ? 0.3 : 1}
						style="transition: opacity 200ms ease"
						onmouseenter={() => (hoveredGroup = line.group)}
						onmouseleave={() => (hoveredGroup = null)}
						ontouchstart={(e) => {
							e.preventDefault();
							hoveredGroup = hoveredGroup === line.group ? null : line.group;
						}}
						role="img"
						aria-label={line.group}
					>
						<!-- Wide transparent hit area -->
						<path d={line.path} stroke="#0087b8" stroke-width="6" fill="none" />
						<path d={line.path} stroke="url(#{line.gradientId})" fill="none" stroke-width="3" />
					</g>
				{/each}
				{#each groupLabels as label}
					{@const dimmed = hoveredGroup !== null && hoveredGroup !== label.group}
					<foreignObject
						x={label.x - 10}
						y={label.y - 5}
						width="300"
						height="24"
						overflow="visible"
						opacity={dimmed ? 0.3 : 1}
						style="transition: opacity 200ms ease"
					>
						<div
							class="pointer-events-none rounded border border-primary-blue bg-white px-1 pt-1 font-epilogue text-xs text-accent-red shadow"
							style="width: fit-content; white-space: nowrap;"
						>
							{label.group}
						</div>
					</foreignObject>
				{/each}
				{#each lineData as line}
					{@const dimmed = hoveredGroup !== null && hoveredGroup !== line.group}
					<foreignObject
						x={line.lastX - 6}
						y={line.lastY - 10}
						width="70"
						height="24"
						opacity={dimmed ? 0.3 : 1}
						style="transition: opacity 200ms ease"
					>
						<div
							class="pointer-events-none rounded border border-primary-blue bg-white px-1 pt-1 font-epilogue text-xs text-accent-red shadow"
							style="width: fit-content; white-space: nowrap;"
						>
							{line.changeLabel}
						</div>
					</foreignObject>
				{/each}
				{#each xTicks as xtick}
					<line
						x1={xScale(xtick)}
						x2={xScale(xtick)}
						y1={height - 35}
						y2={35}
						stroke="white"
						stroke-dasharray="1 2"
					></line>
					<text x={xScale(xtick)} y={height - 10} fill="white" text-anchor="middle">{xtick}</text>
				{/each}
				{#each yTicks as ytick, t}
					<line
						stroke-dasharray="1 2"
						x1={marginX}
						x2={width}
						y1={yScale(ytick) + 5}
						y2={yScale(ytick) + 5}
						stroke="white"
					></line>
					<text x={marginX} y={yScale(ytick)} fill="white">{ytick}%</text>
					{#if t === yTicks.length - 1}
						<text x={marginX + 35} y={yScale(ytick)} text-anchor="start" fill="white" font-size="10"
							>Increase in Risk (%)</text
						>
					{/if}
				{/each}
				<!-- Chart content goes here -->
			{/if}
		</svg>
	</div>
{/if}
