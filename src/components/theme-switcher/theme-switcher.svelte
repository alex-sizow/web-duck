<script>
	import { onMount } from 'svelte';

	export let themes = ['auto', 'light', 'dark'];
	export let currentTheme = 'auto';

	function setupSwitcher() {
		const savedScheme = getSavedScheme();

		if (savedScheme !== null) {
			currentTheme = savedScheme;
		}

		updateMediaQueries();
	}

	function handleThemeChange(event) {
		const newTheme = event.currentTarget.value;
		currentTheme = newTheme;
		setScheme(newTheme);
	}

	function setScheme(scheme) {
		switchMedia(scheme);

		if (scheme === 'auto') {
			clearScheme();
		} else {
			saveScheme(scheme);
		}
	}

	function switchMedia(scheme) {
		let lightMedia;
		let darkMedia;

		if (scheme === 'auto') {
			lightMedia = '(prefers-color-scheme: light)';
			darkMedia = '(prefers-color-scheme: dark)';
		} else {
			lightMedia = scheme === 'light' ? 'all' : 'not all';
			darkMedia = scheme === 'dark' ? 'all' : 'not all';
		}

		lightStyle.media = lightMedia;
		darkStyle.media = darkMedia;

		lightTheme.content = scheme === 'light' ? '#ffffff' : '#000000';
		darkTheme.content = scheme === 'dark' ? '#000000' : '#ffffff';
	}

	function getSavedScheme() {
		return localStorage.getItem('color-scheme');
	}

	function saveScheme(scheme) {
		localStorage.setItem('color-scheme', scheme);
	}

	function clearScheme() {
		localStorage.removeItem('color-scheme');
	}

	function updateMediaQueries() {
		switchMedia(currentTheme);
	}

	onMount(() => {
		setupSwitcher();
	});
</script>

$app/stores;

<meta name="theme-color" bind:this={themeColor} />

<section
	class="theme-switcher"
	class:light={currentTheme === 'light'}
	class:dark={currentTheme === 'dark'}
>
	{#each themes as theme}
		<button
			class="theme-switcher__button"
			aria-pressed={currentTheme === theme ? 'true' : 'false'}
			value={theme}
			on:click={handleThemeChange}
		>
			{theme}
		</button>
	{/each}
	<div class="theme-switcher__status"></div>
</section>

<style>
	:global(body.light) {
		--theme-switcher-back: hsl(var(--color-sulu));
		--theme-switcher-text: hsl(var(--color-ebony));
	}

	:global(body.dark) {
		--theme-switcher-back: hsl(var(--color-ebony));
		--theme-switcher-text: hsl(var(--color-sulu));
	}

	.theme-switcher {
		--theme-switcher-hover-back: hsl(var(--color-sulu));
		--theme-switcher-hover-text: hsl(var(--color-ebony));

		position: relative;
		z-index: 1;
		display: inline-grid;
		grid-template-columns: repeat(3, 1fr);
		padding: 2px;
		border: 2px solid var(--theme-switcher-back);
		border-radius: 24px;
	}

	/* Button */

	.theme-switcher__button {
		margin: 0;
		padding: 0;
		padding-inline: 16px;
		border: none;
		border-radius: 24px;
		background-color: transparent;
		color: var(--theme-switcher-back);
		line-height: inherit;
		font-size: inherit;
		font-family: inherit;
		transition: color 0.1s linear 0.1s;

		&[aria-pressed='true'] {
			outline-offset: 2px;
			color: var(--theme-switcher-text);
		}

		@media (hover: hover) and (pointer: fine) {
			&[aria-pressed='false']:hover {
				animation: menu-button 0.2s both;
			}
		}

		&:focus-visible {
			outline-offset: -2px;
		}
	}

	@keyframes menu-button {
		to {
			background-color: var(--theme-switcher-hover-back);
			color: var(--theme-switcher-hover-text);
		}
	}

	/* Status */

	.theme-switcher__status {
		position: absolute;
		inset: 2px;
		z-index: -1;
		margin-inline: auto;
		width: calc(33% - 0.5px);
		border-radius: 24px;
		background-color: var(--theme-switcher-back);
		pointer-events: none;
		transform: translateX(0);
		transition: transform 0.2s;

		.theme-switcher__button[aria-pressed='true'][value='light'] ~ & {
			transform: translateX(-100%);
		}

		.theme-switcher__button[aria-pressed='true'][value='dark'] ~ & {
			transform: translateX(100%);
		}
	}
</style>
