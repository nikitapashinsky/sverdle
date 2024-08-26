<script lang="ts">
	// TO DO
	// [] Check for double letters!!
	// [] Connect do dictionary API
	// [] Only allow words from dictionary
	// [] Handle win and loss conditions
	// [] Force unique words?
	// [] Listen for key presses instead of text input
	// [] Highlight keys on press
	// [] Add color states to visual keyboard
	// [] Add animations
	//
	//
	// How to handle duplicate letters…
	//

	import { clsx } from 'clsx';

	let targetWord: string = $state('JELLY');
	let currentAttempt: number = $state(1);
	let currentGuess: string = $state('');
	let previousGuesses: string[] = $state([]);

	function countLetter(word: string, letter: string): number {
		return word.split(letter).length - 1;
	}

	function checkInput(event: KeyboardEvent) {
		if (!/^[a-zA-Z]$/.test(event.key)) {
			event.preventDefault();
		}
	}

	function handleSubmit(event: KeyboardEvent) {
		if (event.key === 'Enter' && currentGuess.length === 5 && currentAttempt <= 6) {
			previousGuesses = [...previousGuesses, currentGuess.toUpperCase()];
			console.log(previousGuesses);
			currentGuess = '';
			currentAttempt++;
		}
	}

	const baseTileStyle =
		'flex aspect-square w-16 items-center justify-center rounded-xl text-center text-4xl font-bold uppercase bg-neutral-200';

	function getClasses(wordIndex: number, letterIndex: number) {
		const letter = previousGuesses[wordIndex]?.[letterIndex];
		let letterBudget: number;
		letterBudget = countLetter(targetWord, targetWord[letterIndex]);
		console.log(letterBudget);
		return clsx(
			baseTileStyle,
			{
				'bg-neutral-500 text-white': currentAttempt > wordIndex + 1 && !targetWord.includes(letter)
			},
			{
				'bg-yellow-500': targetWord.includes(letter)
			},
			{
				'bg-green-500 text-white':
					targetWord[letterIndex] === previousGuesses?.[wordIndex]?.[letterIndex]
			}
		);
	}

	function renderPreviousGuess(wordIndex: number, letterIndex: number) {
		return previousGuesses[wordIndex]?.[letterIndex];
	}

	function renderCurrentGuess(letterIndex: number) {
		return [...currentGuess][letterIndex];
	}
</script>

<div class="flex flex-col items-center gap-8">
	<div class="flex flex-col gap-2">
		{#each Array(6) as _, row}
			<div class="flex gap-2">
				{#each Array(5) as _, cell (cell)}
					<div class={getClasses(row, cell)}>
						{#if currentAttempt === row + 1}
							{renderCurrentGuess(cell)}
						{:else if currentAttempt > row + 1}
							{renderPreviousGuess(row, cell)}
						{:else}{/if}
					</div>
				{/each}
			</div>
		{/each}
	</div>

	<div class="flex flex-col items-center gap-2">
		<div class="flex gap-2">
			{#each ['Q', 'W', 'E', 'R', 'T', 'Y', 'U', 'I', 'O', 'P'] as letter}
				<div class={clsx(`flex rounded-lg bg-neutral-200 px-3 py-4 hover:bg-neutral-300`)}>
					{letter}
				</div>
			{/each}
		</div>
		<div class="flex gap-2">
			{#each ['A', 'S', 'D', 'F', 'G', 'H', 'J', 'K', 'L'] as letter}
				<div class="flex rounded-lg bg-neutral-200 px-3 py-4 hover:bg-neutral-300">{letter}</div>
			{/each}
		</div>
		<div class="flex gap-2">
			<div class="flex rounded-lg bg-neutral-200 px-3 py-4 hover:bg-neutral-300">Enter</div>
			{#each ['Z', 'X', 'C', 'V', 'B', 'N', 'M'] as letter}
				<div class="flex rounded-lg bg-neutral-200 px-3 py-4 hover:bg-neutral-300">{letter}</div>
			{/each}
			<div class="flex rounded-lg bg-neutral-200 px-3 py-4 hover:bg-neutral-300">⌫</div>
		</div>
	</div>

	<input
		bind:value={currentGuess}
		onkeypress={checkInput}
		onkeydown={handleSubmit}
		type="text"
		minlength="5"
		maxlength="5"
		class="h-11 rounded-xl bg-neutral-200 px-3 font-semibold uppercase tracking-widest"
	/>
</div>
