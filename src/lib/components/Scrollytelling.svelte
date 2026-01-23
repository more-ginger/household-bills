<script lang="ts">
	import { marked } from 'marked';
	import Text from '../content/test.md?raw';

	const html = marked.parse(Text);
</script>

<h1>Scrollytelling</h1>
<div>{@html html}</div>
