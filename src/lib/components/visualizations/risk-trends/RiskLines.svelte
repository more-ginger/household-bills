<script lang="ts">
	let { selectedFactorData, selectedFactor } = $props();

	let width = $state(0);
	let height = $state(0);

	let hoveredIndex = $state<number | null>(null);

	// Reset tooltip whenever the selected factor changes
	$effect(() => {
		selectedFactor;
		hoveredIndex = null;
	});

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
	<div class="relative h-full min-h-0 flex-1">
		<svg
			class="font-epilogue"
			width="100%"
			height="100%"
			bind:clientWidth={width}
			bind:clientHeight={height}
		>
			{#if width > 0 && height > 0}
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
