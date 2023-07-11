<script lang="ts">
	import Sidebar from '$lib/components/Sidebar.svelte';
	import ContentPane from '$lib/components/ContentPane.svelte';
	import PreviewPanel from '$lib/components/PreviewPanel.svelte';
	import DeviceSelectionModal from '$lib/components/DeviceSelectionModal.svelte';
</script>

<main>
	<Sidebar />
	<PreviewPanel />
	<ContentPane />
	<DeviceSelectionModal />
</main>

<style>
	main {
		display: grid;
		grid-template-rows: 1fr min-content;
		grid-template-columns: 240px 1fr 1fr;
		grid-template-areas:
			'side preview main'
			'side preview main';
		height: 100%;
	}
</style>
