<script lang="ts">
	import RiskBars from './visualizations/risk-change/RiskBars.svelte';
	import data from '$lib/data/data.json';
	import RiskSelector from './visualizations/risk-change/RiskSelector.svelte';

	const formatLabel = (str: string) => {
		const spaced = str.replace(/-/g, ' ');
		return spaced.charAt(0).toUpperCase() + spaced.slice(1);
	};

	const keys = Object.keys(data) as Array<keyof typeof data>;
	let selectedFactor = $state<keyof typeof data>(keys[3]);

	let selectedFactorData = $derived(data[selectedFactor]);
	let selectedFactorDescription = $derived(selectedFactorData.description);
</script>

<section class="bg-primary-blue">
	<div class="m-auto w-6/7 md:w-5/7">
		<div class="mt-5 md:flex md:h-[15vh] md:justify-end">
			<div class="md:h-fit md:w-3/5">
				<h1>{formatLabel(selectedFactor)}</h1>
				<p>{selectedFactorDescription}</p>
			</div>
			<div class="md:w-1/5">Switch visualization</div>
		</div>
		<div class="flex flex-col md:h-fit md:flex-row">
			<div class="order-1 md:order-1 md:w-1/5">
				<div>Risk Factors</div>
				<RiskSelector {data} {keys} bind:selectedFactor />
			</div>
			<div class="order-2 md:order-2 md:w-4/5">
				<RiskBars {selectedFactorData} {selectedFactor} />
			</div>
		</div>
		<div class="text-xs md:w-4/5 md:py-4">
			Authors’ calculations using University of Essex, Institute for Social and Economic Research
			(2023) data, other methodological annotations. These could be longer sentences or URL pointing
			to extrernal resources. Now writing random things.
		</div>
	</div>
</section>
