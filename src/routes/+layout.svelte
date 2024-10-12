<script>
	import './layout.css';
	import Header from '@/components/header/Header.svelte';
</script>

<main>
	<Header />

	<slot />
</main>

<style global>
	.error {
		color: red;
		font-weight: bold;
	}
</style>
