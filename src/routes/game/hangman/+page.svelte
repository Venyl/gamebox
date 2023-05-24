<script lang="ts">
	import '@fontsource/podkova';
	import type { Letter } from '$lib/types';
	import randomWords from 'random-words';
	import hangmanImg1 from '$lib/assets/hangman1.webp';
	import hangmanImg2 from '$lib/assets/hangman2.webp';
	import hangmanImg3 from '$lib/assets/hangman3.webp';
	import hangmanImg4 from '$lib/assets/hangman4.webp';
	import hangmanImg5 from '$lib/assets/hangman5.webp';
	import hangmanImg6 from '$lib/assets/hangman6.webp';
	import hangmanImg7 from '$lib/assets/hangman7.webp';
	import hangmanImg8 from '$lib/assets/hangman8.webp';

	const ALPHABET_ARR = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'.split('') as Letter[];

	function getRandomWord() {
		let word = randomWords(1)[0].toUpperCase();
		while (word.length < 6) {
			word = randomWords(1)[0].toUpperCase();
		}

		return word;
	}

	let winningWord = getRandomWord();
	let currentWord: ('_' | Letter)[] = Array(winningWord.length).fill('_');
	let usedLetters: Letter[] = [];
	let wrongGuesses = 0;

	const hangmanImgs = [
		hangmanImg1,
		hangmanImg2,
		hangmanImg3,
		hangmanImg4,
		hangmanImg5,
		hangmanImg6,
		hangmanImg7,
		hangmanImg8
	];

	let lost = false;
	let won = false;

	function guessLetter(letter: Letter) {
		if (won || lost) return;

		usedLetters = [...usedLetters, letter];
		let wrongLetter = true;

		for (let i = 0; i < winningWord.length; i++) {
			if (letter !== winningWord[i]) continue;
			wrongLetter = false;
			currentWord[i] = letter;
		}

		if (currentWord.join('') === winningWord) {
			won = true;
			return;
		}

		if (wrongLetter) wrongGuesses++;

		if (wrongGuesses === 7) {
			lost = true;
		}
	}

	function restart() {
		winningWord = getRandomWord();
		currentWord = Array(winningWord.length).fill('_');
		usedLetters = [];
		wrongGuesses = 0;
		lost = false;
		won = false;
	}
</script>

<svelte:head>
	<style>
		.body.body {
			background-color: hsl(70, 80%, 90%);
		}
	</style>
</svelte:head>

<div class="container">
	{#if won}
		<h2>You won! The word was: {winningWord}</h2>
	{:else if lost}
		<h2>You lost! The word was: {winningWord}</h2>
	{:else}
		<h2>{currentWord.join('')}</h2>
	{/if}

	<img src={hangmanImgs[wrongGuesses]} alt="Hangman game" />

	<div class="game">
		<div class="letter-btns">
			{#each ALPHABET_ARR as letter}
				<button
					class="letter-btn"
					disabled={usedLetters.includes(letter) || won || lost}
					on:click={() => guessLetter(letter)}>{letter}</button
				>
			{/each}
		</div>
	</div>

	<button class="btn-reset" on:click={restart}>Restart</button>
</div>

<style>
	.container {
		display: flex;
		flex-direction: column;
		gap: 1rem;
	}
	h2 {
		text-align: center;
	}
	img {
		margin: 0 auto;
		max-width: min(250px, 50vw);
	}
	.btn-reset {
		border-radius: 0.5rem;
		box-shadow: 0px 10px 15px -3px rgba(0, 0, 0, 0.15);
		background-color: white;
		font-family: Nunito;
		border: none;
		max-width: 16ch;
		margin-inline: auto;
		padding: 0.3rem 2rem;
	}
	button:enabled:hover {
		transform: scale(1.03);
	}
	.letter-btns {
		display: flex;
		flex-wrap: wrap;
		max-width: min(80vw, 48rem);
		gap: 0.5em;
		justify-content: space-evenly;
		align-content: center;
	}
	.letter-btn {
		font-family: Podkova;
		font-size: min(1.5rem, 4.5vw);
		padding: 0.4em;
		width: 2em;
		aspect-ratio: 1;
		line-height: 0;
		border: none;
		border-radius: 0.5em;
		box-shadow: 0px 5px 8px -2px rgba(0, 0, 0, 0.25);
	}
</style>
