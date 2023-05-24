<script lang="ts">
	import GameSelectionButton from '../components/GameSelectionButton.svelte';
	import tttImg from '$lib/assets/ttt.png';
	import snakeImg from '$lib/assets/snake.png';
	import wordleImg from '$lib/assets/wordle.png';
	import hangmanImg from '$lib/assets/hangman.png';

	const gameDescription =
		'Lorem ipsum dolor sit amet consectetur adipisicing elit. Voluptatem repudiandae soluta corrupti vero non ullam sequi rerum praesentium sint est.';
</script>

<div class="game-selection-buttons">
	<GameSelectionButton
		gameName={'Tic Tac Toe'}
		gameDescription="Two players take turns placing Xs and Os on a 3x3 grid until one gets three in a row."
		gameImgSrc={tttImg}
	/>
	<GameSelectionButton
		gameName={'Snake'}
		gameDescription="Navigate a hungry snake to eat food and grow longer, but avoid colliding with walls or yourself in this classic arcade game."
		gameImgSrc={snakeImg}
	/>
	<GameSelectionButton
		gameName={'Wordle'}
		gameDescription="Guess the secret five-letter word while receiving feedback in this simple yet addictive game."
		gameImgSrc={wordleImg}
	/>
	<GameSelectionButton
		gameName={'Hangman'}
		gameDescription="Guess the hidden word by suggesting letters, but be careful - every incorrect guess brings you closer to hanging."
		gameImgSrc={hangmanImg}
	/>
</div>

<style>
	.game-selection-buttons {
		flex-grow: 1;
		display: grid;
		grid-template-columns: auto auto;
		place-content: center;
		justify-items: center;
		gap: 2rem;
		padding: 2rem;
	}

	@media screen and (max-width: 50rem) {
		.game-selection-buttons {
			grid-template-columns: 1fr;
		}
	}
</style>
