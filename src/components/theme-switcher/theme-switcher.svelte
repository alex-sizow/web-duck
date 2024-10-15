<script>
	import { onMount } from 'svelte';

	export let themes = ['light', 'auto', 'dark'];
	export let currentTheme = getSavedScheme() || 'auto';

	let styleElement;
	let mediaQuery;

	onMount(() => {
		// Создаем styleElement только на клиентской стороне
		styleElement = document.createElement('style');
		document.head.appendChild(styleElement);

		// Инициализация MediaQueryList
		mediaQuery = window.matchMedia('(prefers-color-scheme: dark)');

		function applySystemTheme(isDark) {
			if (isDark) {
				currentTheme = 'dark';
			} else {
				currentTheme = 'light';
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

		const listener = (event) => {
			if (currentTheme === 'auto') {
				applySystemTheme(event.matches);
			}
		};

		// Используйте addEventListener вместо addListener
		mediaQuery.addEventListener('change', listener);

		// Инициализ��ция текущей темы
		setupSwitcher();
	});

	function handleThemeChange(event) {
		const newTheme = event.currentTarget.value;
		currentTheme = newTheme;
		setScheme(newTheme);
	}

	function setScheme(scheme) {
		if (scheme === 'auto') {
			applySystemTheme(mediaQuery.matches);
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
          --background-color: linear-gradient(118deg, rgba(186,187,2,1) 0%, rgba(0,0,0,1) 62%, rgba(16,120,30,1) 100%);
          --text-color: white;
          --accent-color: #007bff;
          --theme-switcher-back: white;
        }
      }
      
      @media (prefers-color-scheme: light) {
      :root {
          --background-color: linear-gradient(118deg, rgba(186,187,2,1) 0%, rgba(197,233,234,1) 62%, rgba(16,120,30,1) 100%);
          --text-color: black;
          --accent-color: #007bff;
        --theme-switcher-back: black;
        }
      }
    `;

		if (currentTheme === 'dark') {
			cssContent += `
      :root {
          --background-color: rgb(186,187,2);
          --background-gradient: linear-gradient(118deg, rgba(186,187,2,1) 0%, rgba(0,0,0,1) 62%, rgba(16,120,30,1) 100%);
          --text-color: white;
          --accent-color: #007bff;
          --theme-switcher-back: white;
          --theme-color: black;
        }
      `;
		} else if (currentTheme === 'light') {
			cssContent += `
      :root {
          --background-color: rgb(186,187,2);
          --background-gradient: linear-gradient(118deg, rgba(186,187,2,1) 0%, rgba(186,224,225,1) 62%, rgba(16,120,30,1) 100%);
          --text-color: black;
          --accent-color: #007bff;
        --theme-switcher-back: black;
          --theme-color: white;
        }
      `;
		}

		if (styleElement) {
			styleElement.textContent = cssContent;
		}
	}

	function setupSwitcher() {
		const savedScheme = getSavedScheme();

		if (savedScheme !== null) {
			currentTheme = savedScheme;
		} else {
			currentTheme = 'auto';
		}

		if (currentTheme === 'auto') {
			applySystemTheme(mediaQuery.matches);
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
	<div class="theme-switcher__status"></div>
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
		color: var(--text-color);
		line-height: inherit;
		font-size: inherit;
		font-family: inherit;
		transition: color 0.1s linear 0.1s;

		&[aria-pressed='true'] {
			outline-offset: 2px;
			color: var(--theme-color);
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

		.theme-switcher__button[aria-pressed='true'][value='auto'] ~ & {
			transform: translateX(0%);
		}
	}
</style>
