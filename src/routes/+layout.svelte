<!-- <script >
  import '../app.css';
</script> -->

<header>
	<a href="/">WEBDUCK</a>
	<nav>
		<ul>
			<li><a href="/articles">Articles</a></li>
			<li><a href="/projects">Projects</a></li>
			<li><a href="/about">About</a></li>
		</ul>
	</nav>
</header>
<main>
	<slot />
</main>

<style global>
	header {
		width: 100%;
		height: 60px;
		border-radius: 0 0 12px 12px;
		background-color: black;
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
			ul {
				display: flex;
				column-gap: 40px;
			}
		}
	}
	:global(body) {
	}
</style>
