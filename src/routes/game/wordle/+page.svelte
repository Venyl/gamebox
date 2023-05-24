<script lang="ts">
	import '@fontsource/podkova';
	import type { Letter, WordleTile } from '$lib/types';
	import getRandomWord from '$lib/words';

	function isOfTypeLetter(text: string): text is Letter {
		const ALPHABET = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ';
		return ALPHABET.includes(text);
	}

	let winningWord = getRandomWord();
	let lost = false;
	let won = false;

	const INITIAL_GAME_STATE: WordleTile[][] = [...Array(6)].map(() =>
		[...Array(5)].map(() => ({ letter: '', state: 'empty' }))
	);

	let gameState: WordleTile[][] = structuredClone(INITIAL_GAME_STATE);

	let currentLetter = 0;
	let currentRow = 0;

	function finalizeRow() {
		currentLetter = 0;

		const currentWordArr = gameState[currentRow] as WordleTile[];
		const winningWordIncorrect = winningWord.split('');
		for (let i = 0; i < 5; i++) {
			if (winningWord.at(i) === currentWordArr[i].letter) {
				currentWordArr[i].state = 'correct';
				winningWordIncorrect.splice(i, 1);
			} else if (winningWordIncorrect.includes(currentWordArr[i].letter)) {
				currentWordArr[i].state = 'present';
			} else {
				currentWordArr[i].state = 'absent';
			}
		}

		currentRow++;
		if (currentWordArr.every((e) => e.state === 'correct')) {
			won = true;
		} else if (currentRow > 5) {
			lost = true;
		}
	}

	function setLetter(letter: Letter) {
		gameState[currentRow][currentLetter++].letter = letter;
		if (currentLetter > 4) finalizeRow();
	}

	function restart() {
		gameState = structuredClone(INITIAL_GAME_STATE);
		winningWord = getRandomWord();
		currentLetter = 0;
		currentRow = 0;
		lost = false;
		won = false;
	}
</script>

<svelte:window
	on:keydown={(e) => {
		const char = e.key.toUpperCase();
		if (!isOfTypeLetter(char) || won || lost) return;
		setLetter(char);
	}}
/>

<svelte:head>
	<style>
		.body.body {
			background-color: hsl(350, 80%, 90%);
		}
	</style>
</svelte:head>

{#if won}
	<h2>You won!</h2>
{:else if lost}
	<h2>You lost! The word was: {winningWord}</h2>
{:else}
	<h2>&nbsp;</h2>
{/if}

<div class="game">
	{#each gameState as row}
		{#each row as wordleTile}
			<div
				class="letter"
				class:correct={wordleTile.state === 'correct'}
				class:present={wordleTile.state === 'present'}
				class:absent={wordleTile.state === 'absent'}
			>
				{wordleTile.letter}
			</div>
		{/each}
	{/each}
</div>
<button class="btn-reset" on:click={restart}>Restart</button>

<style>
	h2 {
		text-align: center;
	}

	.game {
		margin-top: 1rem;
		display: grid;
		grid-template-columns: repeat(5, 1fr);
		grid-auto-rows: 1fr;
		place-content: center;
		gap: min(1rem, 1vw);
	}
	.letter {
		display: grid;
		place-items: center;
		font-size: min(4rem, 5vw);
		font-weight: bold;
		line-height: 0;
		width: 1.5em;
		aspect-ratio: 1;
		background-color: hsla(0, 0%, 100%, 0.2);
		border: 2px solid rgba(0, 0, 0, 0.15);
		transition: background-color 500ms;
	}
	button:disabled {
		color: black;
	}
	.btn-reset {
		border-radius: 0.5rem;
		box-shadow: 0px 10px 15px -3px rgba(0, 0, 0, 0.15);
		background-color: white;
		font-family: Nunito;
		border: none;
		max-width: 16ch;
		margin-top: 2rem;
		margin-inline: auto;
		padding: 0.3rem 2rem;
	}
	button:enabled:hover {
		transform: scale(1.03);
	}

	.present {
		background-color: hsl(60, 90%, 85%);
	}

	.correct {
		background-color: hsl(120, 90%, 85%);
	}

	.absent {
		background-color: hsla(0, 0%, 0%, 0.15);
	}
</style>
