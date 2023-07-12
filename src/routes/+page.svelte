<script lang="ts">
	import Sidebar from '$lib/components/Sidebar.svelte';
	import MainPanel from '$lib/components/MainPanel.svelte';
	import NotePanel from '$lib/components/NotePanel.svelte';
	import DeviceSelectionModal from '$lib/components/DeviceSelectionModal.svelte';
</script>

<main>
	<Sidebar />
	<MainPanel />
	<NotePanel />
	<DeviceSelectionModal />
</main>

<style>
	main {
		display: flex;
		height: 100%;
	}
</style>
