<script lang="ts">
	type GameName = 'Tic Tac Toe' | 'Hangman' | 'Snake' | 'Wordle';
	export let gameName: GameName;
	export let gameDescription: string;
	export let gameImgSrc: string = 'https://placehold.co/600x400';
	$: gamePath = gameName.toLowerCase().replaceAll(' ', '-');
</script>

<a href={`/game/${gamePath}`}>
	<div>
		<img src={gameImgSrc} alt="Game screenshot" />
		<h2>{gameName}</h2>
		<p>{gameDescription}</p>
	</div>
</a>

<style>
	div {
		display: grid;
		grid-auto-flow: row;
		grid-template-rows: 6rem auto auto;
		place-content: center;
		border-radius: 1rem;
		overflow: hidden;
		max-width: 32rem;
		box-shadow: 0px 10px 15px -3px rgba(0, 0, 0, 0.1);
		background-color: hsl(34, 80%, 97%);
	}
	img {
		max-height: 6rem;
		width: 100%;
		object-fit: cover;
	}
	a {
		text-decoration: none;
		color: inherit;
	}
	h2,
	p {
		padding-inline: 1rem;
	}
	h2 {
		font-size: 1.6rem;
		font-family: Nunito;
	}
	p {
		margin-bottom: 1rem;
	}

	a:hover {
		transform: scale(1.05);
	}
</style>
