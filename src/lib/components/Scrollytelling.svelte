<script lang="ts">
	import { marked } from 'marked';
	import Text from '../content/test.md?raw';

	const html = marked.parse(Text);
</script>

<div class="relative">
	<figure class="sticky top-0 h-dvh w-full bg-red-100"></figure>
	<article class="relative">{@html html}</article>
</div>
