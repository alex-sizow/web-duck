<script>
	import ThemeSwitcher from '@/components/theme-switcher/theme-switcher.svelte';
</script>

<header>
	<a href="/">WEBDUCK</a>
	<nav>
		<ul>
			<li><a href="/articles">Articles</a></li>
			<li><a href="/projects">Projects</a></li>
			<li><a href="/about">About</a></li>
		</ul>
		<ThemeSwitcher />
	</nav>
</header>

<style>
	header {
		width: 100%;
		height: 60px;
		border-radius: 0 0 12px 12px;
		backdrop-filter: blur(16px) saturate(180%);
		-webkit-backdrop-filter: blur(16px) saturate(180%);
		background-color: rgba(17, 25, 40, 0.75);
		display: grid;
		grid-template-columns: min-content auto;
		column-gap: 40px;
		position: sticky;
		top: 0;
		z-index: 2;
		color: white;
		padding-block: 18px;
		padding-inline: 18px;

		nav {
			display: grid;
			column-gap: 40px;
			align-items: center;
			grid-template-columns: 1fr max-content max-content;
			ul {
				display: flex;
				column-gap: 40px;
			}
		}
	}
</style>
