<script lang="ts">
	import { marked } from 'marked';
	let { rawScrollyText, onTextRender = undefined } = $props();

	const html = marked.parse(rawScrollyText);

	function evaluateText(node: HTMLElement) {
		setTimeout(() => {
			onTextRender?.({
				isTextRendered: true,
				hasSteps: node.querySelector('#has-steps') ? true : false
			});
		}, 100);

		return {};
	}
</script>

{#await html then html}
	<div use:evaluateText>{@html html}</div>
{/await}
