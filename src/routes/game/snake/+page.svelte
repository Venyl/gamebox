<script lang="ts">
	import { onMount } from 'svelte';

	type Cell = 'snake' | 'food' | null;
	type Coords = [number, number];
	type Snake = {
		direction: Coords;
		position: Coords[];
	};

	const UP: Coords = [-1, 0];
	const RIGHT: Coords = [0, 1];
	const DOWN: Coords = [1, 0];
	const LEFT: Coords = [0, -1];
	const INITIAL_DELAY = 200;
	let timeout: number;
	let btn: HTMLButtonElement;

	const BOARD_SIZE = 20;
	let board: Cell[][] = [...Array(BOARD_SIZE)].map(() => Array(BOARD_SIZE).fill(null));

	const SNAKE_HEAD = 0;
	let snake: Snake = {
		direction: RIGHT,
		position: [[10, 10]]
	};

	$: snake.position.forEach(([x, y]) => {
		board[x][y] = 'snake';
	});

	$: score = snake.position.length - 1;
	let lost = false;

	function randInt(max: number) {
		return Math.floor(Math.random() * Math.floor(max));
	}

	function generateFood(): Coords {
		return [randInt(BOARD_SIZE), randInt(BOARD_SIZE)];
	}
	let food: Coords = generateFood();
	$: {
		board[food[0]][food[1]] = 'food';
		board = board;
	}

	function restart() {
		clearTimeout(timeout);
		btn.blur();
		board = [...Array(BOARD_SIZE)].map(() => Array(BOARD_SIZE).fill(null));
		snake = {
			direction: RIGHT,
			position: [[12, 13]]
		};
		lost = false;
		food = generateFood();

		timeout = setTimeout(() => {
			loop();
		}, INITIAL_DELAY);
	}

	function loop(delay: number = INITIAL_DELAY) {
		const [x, y] = snake.position[SNAKE_HEAD];
		const [dx, dy] = snake.direction;
		const newHead: [number, number] = [dx + x, dy + y];

		const isOutOfBounds = (coordinate: number) => coordinate < 0 || coordinate > BOARD_SIZE - 1;
		if (isOutOfBounds(newHead[0]) || isOutOfBounds(newHead[1])) {
			lost = true;
			return;
		}

		const snakeAteFood = newHead[0] === food[0] && newHead[1] === food[1];
		if (snakeAteFood) {
			snake.position = [food, ...snake.position];
			food = generateFood();
		} else {
			const oldSnakePosition = [...snake.position];
			const lastSnakePiece: Coords = [oldSnakePosition.at(-1)![0], oldSnakePosition.at(-1)![1]];
			snake.position[SNAKE_HEAD] = newHead;
			for (let i = 1; i < snake.position.length; i++) {
				snake.position[i] = oldSnakePosition[i - 1];
			}
			board[lastSnakePiece[0]][lastSnakePiece[1]] = null;

			for (const snakePiece of snake.position.slice(1)) {
				if (snake.position[0][0] === snakePiece[0] && snake.position[0][1] === snakePiece[1]) {
					lost = true;
					return;
				}
			}
		}
		timeout = setTimeout(() => {
			loop(INITIAL_DELAY / (score ** 0.2 || 1));
		}, delay);
	}

	onMount(() => {
		timeout = setTimeout(() => {
			loop();
		}, INITIAL_DELAY);
		return () => clearTimeout(timeout);
	});
</script>

<svelte:window
	on:keydown={(e) => {
		switch (e.key) {
			case 'ArrowLeft':
				if (
					(snake.position?.[1] && snake.position[0][1] > snake.position?.[1]?.[1]) ||
					snake.direction === RIGHT
				)
					break;
				snake.direction = LEFT;
				break;
			case 'ArrowRight':
				if (
					(snake.position?.[1] && snake.position[0][1] < snake.position?.[1]?.[1]) ||
					snake.direction === LEFT
				)
					break;
				snake.direction = RIGHT;
				break;
			case 'ArrowUp':
				if (
					(snake.position?.[1] && snake.position[0][0] > snake.position?.[1]?.[0]) ||
					snake.direction === DOWN
				)
					break;
				snake.direction = UP;
				break;
			case 'ArrowDown':
				if (
					(snake.position?.[1] && snake.position[0][0] < snake.position?.[1]?.[0]) ||
					snake.direction === UP
				)
					break;
				snake.direction = DOWN;
				break;
			case 'r':
				restart();
				break;
		}
	}}
/>

<svelte:head>
	<style>
		.body.body {
			background-color: hsl(110, 80%, 85%);
			overflow: auto;
		}
	</style>
</svelte:head>

{#if lost}
	<h2>You lost!</h2>
{/if}
<h2 class="score">Score <br /> {score}</h2>

<div class="board">
	{#each board as row}
		<div class="row">
			{#each row as cell}
				<div class={`cell ${cell}`} />
			{/each}
		</div>
	{/each}
</div>

<button class="btn-reset" bind:this={btn} on:click={restart}>Restart</button>

<style>
	h2 {
		text-align: center;
	}

	:root {
		--gap: 2px;
		--snake-color: hsla(140, 90%, 35%, 0.9);
		--food-color: hsla(350, 90%, 35%, 0.9);
	}
	.board {
		margin-top: 1rem;
		display: grid;
		place-content: center;
		grid-template-columns: 20fr;
		grid-auto-rows: 1fr;
		min-width: calc(19 * var(--gap) + 30rem);
		gap: var(--gap);
		box-shadow: 0px 10px 15px -3px rgba(0, 0, 0, 0.2);
	}
	.row {
		display: grid;
		grid-template-columns: repeat(20, 1fr);
		gap: var(--gap);
	}
	.cell {
		display: inline-block;
		background-color: rgba(0, 0, 0, 0.5);
		aspect-ratio: 1;
		border-radius: 2px;
	}
	.snake {
		background-color: var(--snake-color);
	}
	.food {
		background-color: var(--food-color);
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

	button:hover {
		transform: scale(1.03);
	}

	@media only screen and (max-width: 34rem) {
		.board {
			min-width: calc(19 * var(--gap) + min(25rem, 80vw));
		}
	}
</style>
