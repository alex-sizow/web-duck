<script>
	import { onMount } from 'svelte';
	import i18next from 'i18next';

	export let languages = ['ru', 'fr'];
	export let currentLanguage;

	function handleLanguageChange(event) {
		const newLanguage = event.currentTarget.value;
		currentLanguage = newLanguage;
		i18next.changeLanguage(newLanguage);
	}

	function getSavedLanguage() {
		try {
			return localStorage.getItem('language');
		} catch (e) {
			console.warn('localStorage недоступен:', e);
			return null;
		}
	}

	function saveLanguage(language) {
		try {
			localStorage.setItem('language', language);
		} catch (e) {
			console.warn('Не удалось сохранить язык в localStorage:', e);
		}
	}

	onMount(() => {
		const savedLanguage = getSavedLanguage();
		if (savedLanguage !== null && languages.includes(savedLanguage)) {
			currentLanguage = savedLanguage;
			i18next.changeLanguage(savedLanguage);
		} else {
			currentLanguage = languages[0];
			i18next.changeLanguage(currentLanguage);
		}
	});
</script>

<section class="language-switcher">
	{#each languages as lang}
		<button
			class="language-switcher__button"
			aria-pressed={currentLanguage === lang ? 'true' : 'false'}
			value={lang}
			on:click={handleLanguageChange}
		>
			{i18next.t(`lang:${lang}`)}
		</button>
	{/each}
</section>

<style>
	.language-switcher {
		display: inline-flex;
		gap: 8px;
		padding: 4px;
		border-radius: 24px;
		background-color: var(--background-color);
	}

	.language-switcher__button {
		padding: 8px 12px;
		border: none;
		border-radius: 20px;
		background-color: transparent;
		color: var(--text-color);
		font-size: 14px;
		cursor: pointer;
		transition: background-color 0.3s ease;

		&[aria-pressed='true'] {
			background-color: var(--accent-color);
			color: white;
		}

		&:hover {
			background-color: rgba(var(--accent-color-rgb), 0.1);
		}
	}
</style>
