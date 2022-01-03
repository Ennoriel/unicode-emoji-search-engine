<script>
	import { emojis } from '$lib/data';
	import ForkMe from '$lib/StarMe.svelte';

	let search = '';
	let filteredEmojis = emojis;

	let copiedEmoji;
	let timeoutId;

	$: searchRe = search.length > 2 ? new RegExp(`(${search})`, 'gi') : undefined;
	$: {
		filteredEmojis = emojis.filter(
			(emoji) =>
				!search ||
				['category', 'subcategory', 'name'].some((attr) => emoji[attr].indexOf(search) !== -1)
		);
	}

	function copy(emoji) {
		if (timeoutId) clearTimeout(timeoutId);
		navigator.clipboard.writeText(emoji);
		copiedEmoji = emoji;
		timeoutId = setTimeout(() => (copiedEmoji = undefined), 1000);
	}
</script>

{#if copiedEmoji}
	<div class="toast">
		emoji {copiedEmoji} copied!
	</div>
{/if}

<div class="list">
	<input bind:value={search} placeholder="Search an emoji" aria-label="Search an emoji"/>
	{#each filteredEmojis as emoji}
		<button class="block" class:double={emoji.width > 165} on:click={() => copy(emoji.emoji)}>
			{emoji.emoji}
		</button>
	{/each}
</div>

<ForkMe />

<style>
	input {
		width: 100%;
		margin: var(--gap);
		height: var(--size);
		background-color: var(--color);
		border: none;
		font-size: 32px;
		text-align: center;
	}
	.list {
		display: flex;
		flex-wrap: wrap;
		justify-content: center;
		gap: var(--gap);
		margin-bottom: var(--gap);
		--gap: 10px;
		--size: 56px;
		--color: #eeeeee;
	}
	.block {
		width: var(--size);
		height: var(--size);
		line-height: var(--size);
		font-size: calc(0.5 * var(--size));
		text-align: center;
		background-color: var(--color);
		border: none;

		cursor: pointer;
	}
	.double {
		width: calc(2 * var(--size) + var(--gap));
	}
	.toast {
		position: fixed;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
		padding: 24px 48px;
		font-size: 24px;
		background-color: black;
		color: white;
	}
</style>
