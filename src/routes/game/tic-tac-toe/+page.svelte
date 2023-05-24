<script lang="ts">
	import '@fontsource/podkova';

	type TileSymbol = 'X' | 'O' | null;

	type TileState = {
		id: number;
		symbol: TileSymbol;
		disabled: boolean;
	};

	const initialState: TileState[] = [1, 2, 3, 4, 5, 6, 7, 8, 9].map((i) => ({
		id: i,
		symbol: null,
		disabled: false
	}));

	let tilesState: TileState[] = JSON.parse(JSON.stringify(initialState));
	let playerSymbol: TileSymbol = 'X';
	let winner: TileSymbol = null;
	let draw = false;

	function isGameOver(): boolean {
		const [t1, t2, t3, t4, t5, t6, t7, t8, t9] = tilesState;
		const firstRow = t1.symbol && t1.symbol === t2.symbol && t2.symbol === t3.symbol;
		const secondRow = t4.symbol && t4.symbol === t5.symbol && t5.symbol === t6.symbol;
		const thirdRow = t7.symbol && t7.symbol === t8.symbol && t8.symbol === t9.symbol;
		const rowWin = firstRow || secondRow || thirdRow;

		const firstColumn = t1.symbol && t1.symbol === t4.symbol && t4.symbol === t7.symbol;
		const secondColumn = t2.symbol && t2.symbol === t5.symbol && t5.symbol === t8.symbol;
		const thirdColumn = t3.symbol && t3.symbol === t6.symbol && t6.symbol === t9.symbol;
		const columnWin = firstColumn || secondColumn || thirdColumn;

		const firstDiagonal = t1.symbol && t1.symbol === t5.symbol && t5.symbol === t9.symbol;
		const secondDiagonal = t3.symbol && t3.symbol === t5.symbol && t5.symbol === t7.symbol;
		const diagonalWin = firstDiagonal || secondDiagonal;

		if (rowWin || columnWin || diagonalWin) {
			tilesState = tilesState.map((tile) => ({ ...tile, disabled: true }));
			winner = playerSymbol;
			return true;
		}

		return false;
	}

	function setSymbol(id: number) {
		let tile = tilesState.find((e) => e.id === id)!;
		tile.symbol = playerSymbol;
		tile.disabled = true;
		tilesState = tilesState;
		if (isGameOver()) return;
		if (tilesState.every((e) => e.disabled === true)) {
			draw = true;
			return;
		}
		playerSymbol = playerSymbol === 'X' ? 'O' : 'X';
	}

	function restart() {
		tilesState = JSON.parse(JSON.stringify(initialState));
		playerSymbol = 'X';
		winner = null;
		draw = false;
	}
</script>

<svelte:head>
	<style>
		.body.body {
			background-color: hsl(210, 80%, 90%);
		}
	</style>
</svelte:head>

<h2>{draw ? 'Draw' : winner ? winner + ' won' : playerSymbol + "'s turn"}</h2>
<div>
	{#each tilesState as { id, symbol, disabled }}
		<button {disabled} on:click={() => setSymbol(id)}>{symbol ?? '\u00a0'}</button>
	{/each}
</div>
<button class="btn-reset" on:click={restart}>Restart</button>

<style>
	h2 {
		font-family: Nunito;
		text-align: center;
	}
	div {
		display: grid;
		grid-template-columns: repeat(3, 1fr);
		place-content: center;
		gap: 0.5rem;
	}
	button {
		border-radius: 0.5rem;
		box-shadow: 0px 10px 15px -3px rgba(0, 0, 0, 0.15);
		background-color: white;
	}
	button:enabled:hover {
		transform: scale(1.03);
	}
	div > button {
		padding: 0.5em;
		width: min(20vw, 8rem);
		aspect-ratio: 1;
		border: none;
		font-size: min(10vw, 5rem);
		font-family: Podkova;
		line-height: 0;
	}
	button:disabled {
		color: black;
	}
	.btn-reset {
		font-family: Nunito;
		border: none;
		max-width: 16ch;
		margin-top: 2rem;
		margin-inline: auto;
		padding: 0.3rem 2rem;
	}
</style>
