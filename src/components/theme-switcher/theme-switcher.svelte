<script>
	let currentTheme = 'light';

	function setTheme(theme) {
		currentTheme = theme;
		localStorage.setItem('theme', currentTheme === 'dark' ? 'dark' : 'light');
	}
</script>

<section class="theme-switcher">
	{#each ['light', 'auto', 'dark'] as theme}
		<button
			class="theme-switcher__button"
			aria-pressed={currentTheme === theme}
			on:click={() => setTheme(theme)}
		>
			{theme}
		</button>
	{/each}
</section>

<style>
	.theme-switcher {
		display: flex;
		gap: 10px;
		border-radius: 20px;
		border: 1px solid white;
		padding: 2px;
	}

	.theme-switcher__button {
		padding: 2px 15px;
		border-radius: 20px;
		color: white;
		border: none;
		text-transform: capitalize;
		&:hover {
			background-color: var(--accent-color);
		}

		&[aria-pressed='true'] {
			background-color: white;
			color: black;
		}
	}
</style>
