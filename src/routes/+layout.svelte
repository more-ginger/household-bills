<script lang="ts">
	import './layout.css';
	import favicon from '$lib/assets/favicon.svg';

	let { children } = $props();
	
	
</script>

<svelte:head><link rel="icon" href={favicon} /></svelte:head>
<div class="bg-primary-blue text-body-white">{@render children()}</div>
