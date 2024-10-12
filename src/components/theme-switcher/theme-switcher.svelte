<script>
	import { onMount } from 'svelte';

	export let themes = ['light', 'auto', 'dark'];
	export let currentTheme = 'auto';

	let styleElement;

	onMount(() => {
		// Создаем styleElement только на клиентской стороне
		styleElement = document.createElement('style');
		document.head.appendChild(styleElement);
		setupSwitcher();
	});

	function handleThemeChange(event) {
		const newTheme = event.currentTarget.value;
		currentTheme = newTheme;
		setScheme(newTheme);
	}

	function setScheme(scheme) {
		if (scheme === 'auto') {
			clearScheme();
		} else {
			saveScheme(scheme);
		}
		updateTheme();
	}

	function getSavedScheme() {
		try {
			return localStorage.getItem('color-scheme');
		} catch (e) {
			console.warn('localStorage недоступен:', e);
			return null;
		}
	}

	function saveScheme(scheme) {
		try {
			localStorage.setItem('color-scheme', scheme);
		} catch (e) {
			console.warn('Не удалось сохранить схему в localStorage:', e);
		}
	}

	function clearScheme() {
		try {
			localStorage.removeItem('color-scheme');
		} catch (e) {
			console.warn('Не удалось очистить схему из localStorage:', e);
		}
	}

	function updateTheme() {
		let cssContent = `
      :root {
        --background-color: white;
        --text-color: black;
        --accent-color: #007bff;
        --theme-switcher-back: black;
      }
      
      @media (prefers-color-scheme: dark) {
        :root {
          --background-color: black;
          --text-color: white;
          --accent-color: #007bff;
          --theme-switcher-back: white;
        }
      }
      
      @media (prefers-color-scheme: light) {
        :root {
          --background-color: white;
          --text-color: black;
          --accent-color: #007bff;
          --theme-switcher-back: black;
        }
      }
    `;

		if (currentTheme === 'dark') {
			cssContent += `
        :root {
          --background-color: black;
          --text-color: white;
          --accent-color: #007bff;
          --theme-switcher-back: white;
        }
      `;
		} else if (currentTheme === 'light') {
			cssContent += `
        :root {
          --background-color: white;
          --text-color: black;
          --accent-color: #007bff;
          --theme-switcher-back: black;
        }
      `;
		}

		// Обновляем стили только ��сли styleElement существует
		if (styleElement) {
			styleElement.textContent = cssContent;
		}
	}

	function setupSwitcher() {
		const savedScheme = getSavedScheme();

		if (savedScheme !== null) {
			currentTheme = savedScheme;
		}

		updateTheme();
	}
</script>

<section class="theme-switcher">
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
</section>

<style>
	.theme-switcher {
		position: relative;
		z-index: 1;
		display: inline-grid;
		grid-template-columns: repeat(3, 1fr);
		padding: 2px;
		border: 2px solid var(--theme-switcher-back);
		border-radius: 24px;
	}

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
			color: var(--text-color);
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
			background-color: var(--theme-switcher-back);
			color: var(--text-color);
		}
	}
</style>
