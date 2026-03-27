<script lang="ts">
	let { data, keys, isFactors, selectedFactor = $bindable() } = $props();

	const formatLabel = (str: string) => {
		const spaced = str.replace(/-/g, ' ');
		return spaced.charAt(0).toUpperCase() + spaced.slice(1);
	};

	const findBenchmarkGroup = function (key: string) {
		const selectedGroup = data[key];
		const benchmarkGroup = selectedGroup.rel[0]?.['ref category (compared to)'];

		return benchmarkGroup ?? 'None';
	};

	function changeSelectedGroup(key: string) {
		selectedFactor = key;
	}
</script>

<div class="flex overflow-x-auto pb-2 md:block md:h-[70vh] md:overflow-y-scroll">
	<div class="flex flex-row gap-2 font-epilogue md:flex-col md:gap-0">
		{#each keys as key}
			<button
				class={`min-h-[44px] flex-shrink-0 rounded-md border px-3 py-2 md:m-2 ${!isFactors && !data[key]['has-trend'] ? 'cursor-not-allowed border-dotted opacity-60' : 'cursor-pointer'} ${selectedFactor === key ? 'bg-secondary-blue text-primary-blue' : ''}`}
				onclick={() => changeSelectedGroup(key)}
				disabled={!isFactors && !data[key]['has-trend']}
			>
				<div class="flex items-center gap-2 md:border-b">
					<div
						class={`$ mb-1 h-[12px] w-[12px] flex-shrink-0 rounded-xl border ${selectedFactor === key || (!isFactors && data[key]['has-trend'] && selectedFactor === key) ? 'bg-red-500' : ''}`}
					></div>
					<div class="font-epilogue whitespace-nowrap md:truncate">{formatLabel(key)}</div>
				</div>
				<div class="hidden pt-2 pl-2 text-left text-xs md:block md:text-wrap">
					Benchmark Group: {formatLabel(findBenchmarkGroup(key))}
				</div>
			</button>
		{/each}
	</div>
</div>
