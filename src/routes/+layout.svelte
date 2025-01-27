<script lang="ts">
	import '../app.css';
	import '../markdown.css';

	import ScreenWakeLock from './ScreenWakeLock.svelte';

	let { children } = $props();
</script>

<main>
	<ScreenWakeLock />

	{@render children()}
</main>
