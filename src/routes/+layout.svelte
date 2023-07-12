<script>
	import '../styles/app.css';
	import '../styles/colors.css';
	import { onMount } from 'svelte';
	import { loadFromCache } from '$lib/util/persistance';
	import { ensureRecordingDir, ensureWaveDir } from '$lib/util/fs';

	onMount(async () => {
		// Create any required directories
		await Promise.all([ensureWaveDir(), ensureRecordingDir()]);
		// Load the state from cache
		await loadFromCache();
	});
</script>

<slot />
