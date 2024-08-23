<script lang="ts">
	// Word list

	// I could store a single JSON or TypeScript file with all of the words?
	// Or I could find an API and fetch the word from there.

	// Game state

	// I need some sort of overall game state. Is game in progress? Is it over? Did user win or lose?
	// I need the current word (word-to-guess) obviously.
	// I need to store the word entered by user, so that I can compare it to the word.
	// I need to store number of attempts, so that game is finished either when the word is guessed correctly or 6 attempts ran out
	// I need to store previously entered words as well so that I can display them and prevent user from entering the same word
	// I have no idea how to test whether the letter is in a wrong place :( and what if there are 2 same letters in the word…

	let targetWord: string = 'world';
	let currentAttempt: number = $state(0);
	let currentGuess: string = $state('');
	let submittedGuess: string = $state('');
	let previousGuesses: Array<string> = $state([]);

	function handleSubmit() {
		// How to move all of these checks out of handleSubmit?
		if (
			currentGuess.length === 5 &&
			previousGuesses.length <= 6 &&
			!previousGuesses.includes(currentGuess)
		) {
			submittedGuess = currentGuess;
			previousGuesses = [...previousGuesses, submittedGuess];
			currentAttempt++;
			currentGuess = '';
			submittedGuess = '';
			console.log(previousGuesses);
		}
	}

	function handleKeyDown(event: KeyboardEvent) {
		if (event.key === 'Enter') {
			event.preventDefault();
			handleSubmit();
		} else if (!/^[a-zA-Z]$/.test(event.key) && !['Backspace', 'Delete'].includes(event.key)) {
			event.preventDefault();
		}
	}
</script>

<!--
User interface

- It's a 5x6 grid.
- Upon user input, entered word is separated into letters and mapped to the 5 squares in the current row.
- The letters are then colored either gray, green, or yellow.
- A text input at the bottom
- (or potentially a visual keyboard for v2)

-->

<div class="flex flex-col gap-2">
	{#each Array(6) as _, i}
		<div class="flex gap-2">
			{#each Array(5) as _, j}
				<div class="aspect-square w-11 bg-neutral-200 rounded-md">
					{#if i < currentAttempt}
						{previousGuesses[i][j]}
					{:else if i === currentAttempt}
						{submittedGuess[j]}
					{:else}
						<!-- Empty cell for future attempts -->
					{/if}
				</div>
			{/each}
		</div>
	{/each}
</div>

<input
	type="text"
	bind:value={currentGuess}
	onchange={handleSubmit}
	minlength="5"
	maxlength="5"
	onkeydown={handleKeyDown}
	class="bg-neutral-200 rounded-md"
/>
