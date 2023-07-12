<script lang="ts">
	import { convertFileSrc } from '@tauri-apps/api/tauri';
	import Download from 'svelte-icons/fa/FaFileDownload.svelte';
	import { active, output } from '$lib/stores/transcripts';
	import { playback } from '$lib/stores/playback';
	import { isVideo } from '$lib/util/fs';
	import Button from './Button.svelte';
	import { onMount } from 'svelte';
	import { save } from '@tauri-apps/api/dialog';
	import { writeTextFile } from '@tauri-apps/api/fs';
	import { downloadDir } from '@tauri-apps/api/path';
	import ContentPanel from './ContentPanel.svelte';

	let menuOpen = false;

	onMount(() => ($playback.currentTime = 0));

	async function handleSave(name: string, content: string, format: string) {
		const defaultPath = await downloadDir();
		const path = await save({
			title: 'Save transcription',
			defaultPath: `${defaultPath}${name}.${format}`,
			filters: [{ name, extensions: [format] }]
		});
		if (path) writeTextFile(path, content);
		menuOpen = false;
	}
</script>

<div class="main-panel">
	{#if $active && $active.status === 'transcribed'}
		<header>
			<h2>{$active.name}</h2>
			<div class="button-groups">
				<div class="dropdown">
					<Button on:click={() => (menuOpen = !menuOpen)}>
						<Download slot="icon" />
						Export
					</Button>
					<div class="dropdown-content" class:show={menuOpen}>
						<button
							on:click={() => {
								if ($active) handleSave($active.name, $output.text, 'txt');
							}}
						>
							Export plain text
						</button>
						<button
							on:click={() => {
								if ($active) handleSave($active.name, $output.vtt, 'vtt');
							}}
						>
							Export vtt
						</button>
					</div>
				</div>
			</div>
		</header>
		{#if isVideo($active.file.originalPath)}
			<video controls bind:currentTime={$playback.currentTime} bind:paused={$playback.paused}>
				<!-- src="/media/examples/friday.vtt" -->
				<track default kind="captions" srclang="en" />
				<source
					src={convertFileSrc($active.file.originalPath)}
					type={`video/${$active.file.extension.replace('.', '')}`}
				/>
			</video>
		{:else}
			<audio
				src={convertFileSrc($active.file.originalPath || $active.file.transformedPath)}
				controls
				bind:currentTime={$playback.currentTime}
				bind:paused={$playback.paused}
			/>
		{/if}
	{/if}
	<ContentPanel />
</div>

<style>
	.main-panel {
		flex: 1;
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 8px;
		padding: 12px;
		background-color: var(--neutral-200);
		border-top: 1px solid var(--neutral-400);
		backdrop-filter: blur(7px);
		-webkit-backdrop-filter: blur(7px);
	}

	header {
		width: 100%;
		display: flex;
		justify-content: space-between;
		align-items: center;
	}

	h2 {
		margin: 0;
		padding: 5px;
	}

	video,
	audio {
		width: 100%;
	}

	audio {
		margin-top: 40px;
	}

	.button-groups {
		display: flex;
		gap: 5px;
		align-items: center;
		justify-content: left;
	}

	.dropdown {
		position: relative;
	}

	.dropdown-content {
		display: none;
		position: absolute;
		right: 0;
		width: 150px;
		z-index: 1;
	}

	.dropdown-content button {
		width: 100%;
		cursor: pointer;
		padding: 5px 0;
		border: 1px solid var(--neutral-500);
		background: var(--neutral-400);
		color: var(--neutral-900);
	}

	.dropdown-content button:hover {
		background-color: var(--neutral-500);
	}

	.show {
		display: block;
	}
</style>
