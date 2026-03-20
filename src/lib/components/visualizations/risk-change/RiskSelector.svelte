<script lang="ts">
	let { data, keys, selectedFactor = $bindable() } = $props();

	const capitalize = (str: string) => str.charAt(0).toUpperCase() + str.slice(1);

	const findBenchmarkGroup = function (key: string) {
		const selectedGroup = data[key];
		const benchmarkGroup = selectedGroup.rel[0]?.['ref category (compared to)'];

		return benchmarkGroup ?? 'None';
	};

	function changeSelectedGroup(key: string) {
		selectedFactor = key;
	}
</script>

<div class="h-[20vh] overflow-scroll md:h-[70vh]">
	<div class="">
		{#each keys as key}
			<button
				class={`m-2 rounded-md border ${selectedFactor === key ? 'bg-secondary-blue text-primary-blue' : ''}`}
				onclick={() => changeSelectedGroup(key)}
			>
				<div class="flex items-center border-b pl-2">
					<div
						class={`mr-2 h-[15px] w-[15px] rounded-xl border  ${selectedFactor === key ? 'bg-red-500' : ''}`}
					></div>
					<div>{capitalize(key)}</div>
				</div>
				<div class="pl-2">Benchmark Group: {findBenchmarkGroup(key)}</div>
			</button>
		{/each}
	</div>
</div>
