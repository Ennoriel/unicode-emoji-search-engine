<script>
	import { emojis } from '$lib/data';
	import ForkMe from '$lib/StarMe.svelte';
	import { page } from '$app/stores';
	import { onMount } from 'svelte';
	import { browser } from '$app/env';

	export let search = '';
	let filteredEmojis;

	let copiedContent;
	let timeoutId;

	$: searchRe = search.length > 2 ? new RegExp(`(${search})`, 'gi') : undefined;
	$: {
		filteredEmojis = emojis.filter(
			(emoji) =>
				!search ||
				['category', 'subcategory', 'name'].some((attr) => emoji[attr].indexOf(search) !== -1)
		);
	}

	function copy(type, emoji) {
		if (timeoutId) clearTimeout(timeoutId);
		navigator.clipboard.writeText(emoji);
		copiedContent = type === 'emoji' ? 'emoji ' + emoji : 'url';
		timeoutId = setTimeout(() => (copiedContent = undefined), 2000);
	}

	onMount(() => {
		if (browser) search = $page.url.searchParams.get('i');
	});
</script>

{#if copiedContent}
	<div class="toast">
		{copiedContent} copied!
	</div>
{/if}

<div class="list">
	<div class="search-bar">
		<a
			class="block"
			href="?i={search}"
			on:click={() => copy('url', `${$page.url.origin}/?i=${search}`)}>🔗</a
		>
		<input bind:value={search} placeholder="Search an emoji" aria-label="Search an emoji" />
	</div>
	<div class="icons">
		{#each filteredEmojis as emoji (emoji)}
			<button
				class="block"
				class:double={emoji.width > 165}
				on:click={() => copy('emoji', emoji.emoji)}
			>
				{emoji.emoji}
			</button>
		{/each}
	</div>
</div>

<ForkMe />

<style>
	.search-bar {
		display: flex;
		width: 100%;
		gap: var(--gap);
		padding: var(--gap);
		box-sizing: border-box;
		position: fixed;
		background-color: white;
	}
	a {
		text-decoration: none;
	}
	a:focus-visible {
		outline: 2px solid blue;
	}
	input {
		width: 100%;
		height: var(--size);
		background-color: var(--color);
		border: none;
		font-size: 32px;
		text-align: center;
		padding: 0;
	}
	.list {
		--gap: 10px;
		--size: 56px;
		--color: #eeeeee;
	}
	.icons {
		display: flex;
		flex-wrap: wrap;
		justify-content: center;
		gap: var(--gap);
		padding: calc(var(--size) + 2 * var(--gap) + 3px) 0 var(--gap);
		margin: 0 var(--gap);
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
		width: max-content;
	}
</style>
