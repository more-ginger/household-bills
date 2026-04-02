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

	// ── Label helpers ─────────────────────────────────────────────────────────
	let hoveredIndex = $state<number | null>(null);

	// Reset tooltip whenever the selected factor changes
	$effect(() => {
		selectedFactor;
		hoveredIndex = null;
	});

	const LABEL_FONT_SIZE = 12;
	const LABEL_CHAR_PX = 6; // approximate px width per character at font-size 12

	function truncateLabel(label: string, availablePx: number): string {
		// Single-word labels are always rendered in full
		if (!label.includes(' ')) return label;
		const maxChars = Math.floor(availablePx / LABEL_CHAR_PX);
		if (maxChars <= 1) return '';
		return label.length <= maxChars ? label : label.slice(0, maxChars - 1) + '…';
	}

	// ── Responsive layout helpers ────────────────────────────────────────────
	// Below 700px the illustration is hidden, so reclaim that right margin for bars
	const xRangeEnd = $derived(width < 700 ? width : width - marginX * 2);
	// Proportional top/bottom padding so bars never touch the SVG edges
	const yPad = $derived(Math.max(20, height * 0.05));

	// ── Scales ────────────────────────────────────────────────────────────────
	const maxPositiveNumber = $derived(
		max(
			selectedFactorData.rel.map((d: { riskvaluetosplot_1dp: number }) => {
				return +d.riskvaluetosplot_1dp !== 0 ? +d.riskvaluetosplot_1dp : 80;
			})
		)
	);

	const minNegativeNumber = $derived(
		min(
			selectedFactorData.rel.map((d: { riskvaluetosplot_1dp: number }) => {
				return +d.riskvaluetosplot_1dp !== 0 ? +d.riskvaluetosplot_1dp : -80;
			})
		)
	);

	const absMax = $derived(
		Math.max(Math.abs(maxPositiveNumber ?? 0), Math.abs(minNegativeNumber ?? 0))
	);

	const xScale = $derived(
		scaleBand()
			.domain(selectedFactorData.rel.map((d: {}, i: number) => i))
			.range([marginX, xRangeEnd])
			.padding(0.4)
	);

	const yScale = $derived(
		scaleLinear()
			.domain([-absMax * 1.15, absMax * 1.15])
			.range([height - yPad, yPad])
			.nice()
	);

	const baseline = $derived(yScale(0));

	const yTicks = $derived(width > 0 && height > 0 ? yScale.ticks() : []);

	// ── Bar data ──────────────────────────────────────────────────────────────
	const barShapesForChart = $derived(
		selectedFactorData.rel.map(
			(d: { riskvaluetosplot_1dp: number; variable: string }, i: number) => {
				const val = +d.riskvaluetosplot_1dp;
				const y2 = yScale(val);
				const base = yScale(0);
				const rawTextY = val < 0 ? y2 + 20 : y2 - 10;
				const available = Math.max(0, Math.abs(y2 - base) - 20);
				const variable = (d.variable ?? '').trim();
				return {
					y2,
					// Clamped pill anchor — keeps value pills inside the wrapper bounds
					pillY: Math.max(yPad + 20, Math.min(height - yPad - 20, y2)),
					x: xScale(i)! + xScale.bandwidth() / 2,
					value: val,
					isNegative: val < 0,
					textY: Math.max(25, Math.min(rawTextY, height - 5)),
					variable,
					truncatedLabel: truncateLabel(variable, available)
				};
			}
		)
	);

	// ── Validity guard ────────────────────────────────────────────────────────
	const hasValidData = $derived(
		selectedFactorData.rel.length > 0 &&
			selectedFactorData.rel.some(
				(d: { riskvaluetosplot_1dp: number }) => !isNaN(+d.riskvaluetosplot_1dp)
			)
	);
</script>

{#if !hasValidData}
	<div class="flex h-full min-h-0 flex-1 items-center justify-center">
		<p class="text-white opacity-60">No data available for this risk factor.</p>
	</div>
{:else}
	<!-- Wrapper needed so the absolute-positioned overlays are relative to the chart -->
	<div class="relative h-full min-h-0 flex-1">
		<svg
			class="font-epilogue"
			width="100%"
			height="100%"
			bind:clientWidth={width}
			bind:clientHeight={height}
		>
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
							<!-- Risk direction labels hidden on narrow screens — too long to render cleanly -->
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
				<!-- Baseline annotation hidden on narrow screens -->
				{#if width >= 500}
					<text x={width} y={baseline + 15} text-anchor="end" fill="white" font-size="10"
						>No meaningful difference from benchmark group (%)</text
					>
				{/if}
			{/if}

			{#each barShapesForChart as bar, i}
				<!-- svelte-ignore a11y_mouse_events_have_key_events -->
				<g
					onmouseover={() => (hoveredIndex = i)}
					onmouseleave={() => (hoveredIndex = null)}
					ontouchstart={(e) => {
						e.preventDefault();
						hoveredIndex = hoveredIndex === i ? null : i;
					}}
					role="img"
					aria-label={bar.variable}
					style="cursor: default"
				>
					<!-- Transparent hit area covering the full bar span -->
					<rect
						x={bar.x - 20}
						y={Math.min(bar.y2, baseline)}
						width={40}
						height={Math.max(1, Math.abs(bar.y2 - baseline))}
						fill="transparent"
					/>

					<!-- Bar arrow -->
					<line
						y2={bar.y2}
						x2={bar.x}
						y1={baseline}
						x1={bar.x}
						stroke="white"
						stroke-width="3"
						marker-end={bar.value !== 0 ? 'url(#positive-arrowhead)' : 'url(#zero-arrowhead)'}
					/>

					<!--
						Rotated variable label — anchored at the baseline, reading along the bar:
						• positive bars (bar goes up):   text-anchor="end"   → text flows upward toward tip
						• negative bars (bar goes down): text-anchor="start" → text flows downward toward tip
					-->
					{#if bar.truncatedLabel && !(hoveredIndex === i)}
						<text
							x={bar.x - 5}
							y={bar.isNegative ? baseline - 10 : baseline + 10}
							transform={`rotate(90, ${bar.x - 5}, ${bar.isNegative ? baseline - 10 : baseline + 10})`}
							text-anchor={bar.isNegative ? 'end' : 'start'}
							fill="white"
							font-size={LABEL_FONT_SIZE}
							opacity={hoveredIndex === i ? 1 : 0.6}>{bar.truncatedLabel}</text
						>
					{/if}
				</g>
			{/each}
			{#if width > 700}
				<image
					href={`illustrations/webp/humans/${FACTOR_ICONS[selectedFactor] ?? DEFAULT_FACTOR_ICON}`}
					height="70"
					width="70"
					x={width - 70}
					y={baseline - 70}
				/>
			{/if}
		</svg>
		<!-- Always-visible numeric value pills, one per bar.
		     pillY is clamped so pills never escape the wrapper's top/bottom edge. -->
		{#each barShapesForChart as bar}
			<div
				class="pointer-events-none absolute z-10 rounded border border-primary-blue bg-white px-1 pt-1 font-epilogue text-xs text-accent-red shadow"
				style="left: {bar.x}px; top: {bar.pillY}px; transform: translate(-50%, {bar.isNegative
					? '15px'
					: 'calc(-100% - 15px)'})"
			>
				<p>{bar.value}%</p>
			</div>
		{/each}

		<!-- Variable tooltip — shown on hover (desktop) or tap (mobile).
		     left is clamped so the 200px-wide tooltip never overflows the wrapper edges. -->
		{#if hoveredIndex !== null}
			{@const bar = barShapesForChart[hoveredIndex]}
			<div
				class="pointer-events-none absolute z-10 max-w-[200px] rounded bg-white px-2 py-1 text-center font-epilogue text-xs text-accent-red shadow"
				style="left: {Math.max(
					100,
					Math.min(width - 100, bar.x)
				)}px; top: {baseline}px; transform: translate(-50%, {bar.isNegative
					? '8px'
					: 'calc(-100% - 10px)'})"
			>
				{bar.variable}
			</div>
		{/if}
	</div>
{/if}
