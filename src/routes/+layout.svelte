<script>
	import Header from "./Header.svelte";
	import "../app.css";
</script>

<div class="app">
	<Header />

	<main>
		<slot />
	</main>
</div>

<style>
	.app {
		display: flex;
		flex-direction: column;
		justify-content: center;
		min-height: 100vh;
		align-items: center;
		background-color: white;
	}

	main {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		width: 75%;
		max-width: 100vh;
		height: auto;
		max-height: 100vh;
		aspect-ratio: 4 / 3;
		background-repeat: no-repeat;
		background-size: contain;
		background-image: url("../lib/images/background.png");
		box-sizing: border-box;
		margin: 0 auto;
	}
</style>
