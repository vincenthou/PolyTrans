<script lang="ts">
	import Line from '$lib/components/Line.svelte';
	import { active, transcripts } from '$lib/stores/transcripts';
	import Button from './Button.svelte';
</script>

<div class="content-panel">
	{#if $active}
		{@const { status, file, editedOutput } = $active}
		{#if status === 'transcribed'}
			<div class="transcribed-lines">
				{#each editedOutput as line, index}
					<Line {line} {file} {index} />
				{/each}
			</div>
		{:else}
			<div class="transcribe-ready">
				{#if status === 'empty'}
					<p>File ready to be transcribed, audio file will be stored here: {file.audioPath}</p>
					<Button on:click={() => transcripts.startTranscription(file)}>Start Transcribing</Button>
				{:else if status === 'error'}
					Something went wrong
				{:else if status === 'transcribing'}
					Transcribing...
				{/if}
			</div>
		{/if}
	{:else}
		<div class="transcribe-ready">Select a transcript</div>
	{/if}
</div>

<style>
	.content-panel {
		width: 100%;
		height: 100%;
		overflow-y: auto;
		overflow-x: hidden;
		position: relative;
	}

	.transcribe-ready {
		display: flex;
		flex-direction: column;
		gap: 20px;
		align-items: center;
		justify-content: center;
		text-align: center;
		height: 100%;
	}
</style>
