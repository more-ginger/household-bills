<script lang="ts">
	import RiskBars from './visualizations/risk-change/RiskBars.svelte';
	import data from '$lib/data/data.json';
	import RiskSelector from './visualizations/risk-change/RiskSelector.svelte';

	const capitalize = (str: string) => str.charAt(0).toUpperCase() + str.slice(1);

	const keys = Object.keys(data) as Array<keyof typeof data>;
	let selectedFactor = $state<keyof typeof data>(keys[0]);

	let selectedFactorDescription = $derived(data[selectedFactor].description);
</script>

<section class="min-h-dvh border bg-primary-blue">
	<div class="m-auto w-6/7 md:w-5/7">
		<div class="title mt-5 h-[20vh] bg-red-100 md:h-fit">
			<h1>Selected Risk Factor: {capitalize(selectedFactor)}</h1>
			<p>{selectedFactorDescription}</p>
		</div>
		<div class="flex h-[75vh] flex-col border md:h-fit md:flex-row">
			<div class="order-2 md:order-1 md:w-1/5">
				<RiskSelector {data} {keys} bind:selectedFactor />
			</div>
			<div class="order-1 md:order-2 md:w-4/5">
				<RiskBars />
			</div>
		</div>
	</div>
</section>
