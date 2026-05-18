<script lang="ts">
	import RiskBars from './visualizations/risk-change/RiskBars.svelte';
	import data from '$lib/data/data.json';
	import RiskSelector from './visualizations/risk-change/RiskSelector.svelte';
	import RiskLines from './visualizations/risk-trends/RiskLines.svelte';
	import { browser } from '$app/environment';
	import { afterNavigate } from '$app/navigation';

	const formatLabel = (str: string) => {
		const spaced = str.replace(/-/g, ' ');
		return spaced.charAt(0).toUpperCase() + spaced.slice(1);
	};

	const keys = Object.keys(data) as Array<keyof typeof data>;
	let selectedFactor = $state<keyof typeof data>(keys[0]);

	function readVizParam(): boolean {
		return !browser || new URLSearchParams(window.location.search).get('viz') !== 'trends';
	}

	let isFactors = $state(readVizParam());

	// Sync state when header links navigate in with ?viz= already in the URL
	afterNavigate(() => {
		isFactors = readVizParam();
	});

	function setViz(factors: boolean) {
		isFactors = factors;
		if (browser) {
			const url = new URL(window.location.href);
			url.searchParams.set('viz', factors ? 'factors' : 'trends');
			window.history.replaceState({}, '', url.toString());
		}
	}

	let selectedFactorData = $derived(data[selectedFactor]);
	let selectedFactorDescription = $derived(selectedFactorData.description);
</script>

<section id="visualizations" class="bg-primary-blue">
	<div class="m-auto w-6/7 pb-8 md:w-6/7">
		<div class="flex flex-col md:h-fit md:flex-row">
			<div class="md:w-1/5">
				<div class="mb-6 flex w-full rounded-3xl border md:mb-2">
					<button
						class={`grow cursor-pointer rounded-3xl ${isFactors ? 'bg-white text-primary-blue' : ' bg-primary-blue text-white'} p-2`}
						onclick={() => setViz(true)}>Risk Factors</button
					>
					<button
						class={`grow cursor-pointer rounded-3xl ${isFactors ? ' bg-primary-blue text-white' : 'bg-white text-primary-blue'} p-2`}
						onclick={() => setViz(false)}>Risk Trends</button
					>
				</div>
				<RiskSelector {data} {keys} {isFactors} bind:selectedFactor />
			</div>
			<div class="flex h-[100vh] flex-col md:h-[75vh] md:w-4/5">
				<div class="shrink-0 md:flex md:pl-16">
					<div class="my-4 md:my-0 md:h-fit md:w-4/5">
						<h1>{formatLabel(selectedFactor)}</h1>
						<p>{selectedFactorDescription}</p>
					</div>
				</div>

				<div class="my-4 h-full md:my-0">
					{#if isFactors}
						<RiskBars {selectedFactorData} {selectedFactor} />
					{:else}
						<RiskLines {selectedFactorData} {selectedFactor} />
					{/if}
				</div>
			</div>
		</div>
		<div class="text-xs md:w-4/5 md:py-6">
			Data: University of Essex, Institute for Social and Economic Research (2023). Authors’
			calculations.
		</div>
	</div>
</section>
