<script lang="ts">
	import { transcripts } from '$lib/stores/transcripts';
	import { playback } from '$lib/stores/playback';
	import { parseRawOutput, getTimeRangeString } from '$lib/util/timecode';
	import Play from 'svelte-icons/fa/FaPlay.svelte';
	import type { MediaFile } from '$lib/models/mediaFile';
	export let line: string;
	export let file: MediaFile;
	export let index: number;

	$: ({ start, end, text, isEmpty, startSeconds, endSeconds } = parseRawOutput(line));
	$: editedText = text.trim();
	$: active = $playback.currentTime > startSeconds && $playback.currentTime < endSeconds;
	function handleJump() {
		$playback.currentTime = startSeconds;
		$playback.paused = true;
	}
	function handleSeek() {
		$playback.currentTime = startSeconds;
		$playback.paused = false;
	}
	function handleTextChange(e: Event) {
		const textareaElement = e.target as HTMLTextAreaElement;
		transcripts.setEditedOutput(
			file,
			index,
			`[${getTimeRangeString(start, end)}]   ${textareaElement.value}`
		);
	}
</script>

{#if isEmpty === false}
	<div class="line" class:active>
		<button class="timestamp" on:click={handleJump}>
			{start.hoursStr}:{start.minutesStr}:{start.secondsStr} → {end.hoursStr}:{end.minutesStr}:{end.secondsStr}
		</button>

		<textarea class="text" value={editedText} on:change={handleTextChange} />

		<div class="menu">
			<button class="line-button" on:click={handleSeek}>
				<Play />
			</button>
		</div>
	</div>
{/if}

<style>
	.line {
		display: flex;
		padding: 2px 12px;
		gap: 12px;
	}

	.line:hover {
		background-color: var(--neutral-300);
	}

	.line.active {
		background-color: var(--blue-500);
	}

	.timestamp {
		cursor: pointer;
		flex-shrink: 0;
		text-align: center;
		justify-self: flex-start;
		align-self: flex-start;
		display: inline-block;
		padding: 4px;
		border-radius: 5px;
		background-color: var(--neutral-300);
		color: var(--neutral-600);
		font-size: 10px;
		line-height: 1;
	}

	.text {
		height: 22px;
		color: var(--neutral-900);
		flex-grow: 1;
		display: inline-block;
		appearance: none;
		background: transparent;
		border: 0;
		font-size: 15px;
		text-align: left;
		resize: none;
	}

	.menu {
		opacity: 0;
		flex-shrink: 0;
	}

	.line:hover .menu {
		opacity: 1;
	}

	.line-button {
		appearance: none;
		display: inline-block;
		color: var(--neutral-900);
		border: 0;
		background-color: transparent;
		width: 18px;
		height: 18px;
		padding: 2px 4px;
		line-height: 1;
		border-radius: 3px;
		margin: 0;
		cursor: pointer;
	}

	.line-button:hover {
		background-color: var(--neutral-400);
	}
</style>
