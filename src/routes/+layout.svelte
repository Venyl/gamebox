<script lang="ts">
	import '$lib/global.css';
	import '@fontsource/nunito/900.css';
	import '@fontsource/alef';

	export const ssr = false;
</script>

<div class="body">
	<header>
		<h1><a href="/">GameBox</a></h1>
	</header>
	<main>
		<slot />
	</main>
	<footer>Made by Venyl</footer>
</div>

<style>
	.body {
		min-height: 100vh;
		min-height: 100dvh;
		display: flex;
		flex-direction: column;
		font-family: Alef;
		background-color: antiquewhite;
	}
	header {
		background-color: white;
		box-shadow: 0px 10px 15px -3px rgba(0, 0, 0, 0.1);
		padding: 0.5rem 1rem;
	}
	a {
		color: inherit;
		text-decoration: none;
	}

	h1 {
		font-family: Nunito;
		font-weight: 900;
		font-size: 2.5rem;
	}
	main {
		flex-grow: 1;
		display: flex;
	}
	footer {
		font-weight: bold;
		text-align: center;
	}
</style>
