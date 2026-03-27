<script lang="ts">
	import RiskBars from './visualizations/risk-change/RiskBars.svelte';
	import data from '$lib/data/data.json';
	import RiskSelector from './visualizations/risk-change/RiskSelector.svelte';
	import RiskLines from './visualizations/risk-trends/RiskLines.svelte';

	let isFactors = $state(true);

	const formatLabel = (str: string) => {
		const spaced = str.replace(/-/g, ' ');
		return spaced.charAt(0).toUpperCase() + spaced.slice(1);
	};

	const keys = Object.keys(data) as Array<keyof typeof data>;
	let selectedFactor = $state<keyof typeof data>(keys[0]);

	let selectedFactorData = $derived(data[selectedFactor]);
	let selectedFactorDescription = $derived(selectedFactorData.description);
</script>

<section class="bg-primary-blue">
	<div class="m-auto w-6/7 pb-8 md:w-6/7">
		<div class="flex flex-col md:h-fit md:flex-row">
			<div class="md:w-1/5">
				<div class="mb-2 flex w-full rounded-3xl border">
					<button
						class={`grow rounded-3xl ${isFactors ? 'bg-white text-primary-blue' : ' bg-primary-blue text-white'} p-2`}
						onclick={() => (isFactors = true)}>Risk Factors</button
					>
					<button
						class={`grow rounded-3xl ${isFactors ? ' bg-primary-blue text-white' : 'bg-white text-primary-blue'} p-2`}
						onclick={() => (isFactors = false)}>Risk Trends</button
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
			Authors’ calculations using University of Essex, Institute for Social and Economic Research
			(2023) data, other methodological annotations. These could be longer sentences or URL pointing
			to extrernal resources. Now writing random things.
		</div>
	</div>
</section>
