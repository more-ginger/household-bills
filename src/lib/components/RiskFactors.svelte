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
	<div class="m-auto w-6/7 pb-8 md:w-5/7">
		<div class="flex flex-col md:h-fit md:flex-row">
			<div class="md:w-1/5">
				<div
					class="my-6 flex h-[3rem] w-full justify-self-center rounded-4xl bg-white p-4 font-epilogue leading-[1.3rem] font-normal text-primary-blue uppercase md:m-0"
				>
					Risk Factors
				</div>
				<RiskSelector {data} {keys} bind:selectedFactor />
			</div>
			<div class="flex h-[100vh] flex-col md:h-[75vh] md:w-4/5">
				<div class="shrink-0 md:flex md:pl-16">
					<div class="my-4 md:my-0 md:h-fit md:w-4/5">
						<h1>{formatLabel(selectedFactor)}</h1>
						<p>{selectedFactorDescription}</p>
					</div>
					<div class="bg-red-100 md:w-1/5">Switch visualization</div>
				</div>
				<div class="my-4 h-full md:my-0">
					<RiskBars {selectedFactorData} {selectedFactor} />
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
