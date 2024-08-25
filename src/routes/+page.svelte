<script lang="ts">
	import { clsx } from 'clsx';

	let targetWord: string = $state('WORLD');
	let currentAttempt: number = $state(1);
	let currentGuess: string = $state('');
	let previousGuesses: object[] = $state([]);

	function checkInput(event: KeyboardEvent) {
		if (!/^[a-zA-Z]$/.test(event.key)) {
			event.preventDefault();
		}
	}

	function handleSubmit(event: KeyboardEvent) {
		if (event.key === 'Enter' && currentGuess.length === 5 && currentAttempt <= 6) {
			console.log('Handling submit…');

			console.log('Creating word object AND calling handleGuess…');
			handleGuess(createWordObj(currentGuess), targetWord);

			console.log('Resetting currentGuess…');
			currentGuess = '';

			console.log('Increasing currentAttempt…');
			currentAttempt++;
			console.log(currentAttempt);
		}
	}

	function createWordObj(
		guessedWord: string
	): { letter: string; isInWord: boolean; isInCorrectPosition: boolean }[] {
		return [...guessedWord].map((letter) => ({
			letter: letter.toUpperCase(),
			isInWord: false,
			isInCorrectPosition: false
		}));
	}

	function handleGuess(
		guessedWord: { letter: string; isInWord: boolean; isInCorrectPosition: boolean }[],
		targetWord: string
	) {
		console.log('Handling guess…');
		const checkedWord = checkWord(guessedWord, targetWord);
		console.log('Adding checked word to previousGuesses…');
		previousGuesses = [...previousGuesses, checkedWord];
		console.log(previousGuesses);
	}

	function checkWord(
		guessedWord: { letter: string; isInWord: boolean; isInCorrectPosition: boolean }[],
		targetWord: string
	) {
		const checkedWord: object[] = guessedWord.map((letter, index) => ({
			...letter,
			isInWord: targetWord.includes(letter.letter),
			isInCorrectPosition: targetWord[index] === letter.letter
		}));
		return checkedWord;
	}

	function getClasses(wordIndex: number, letterIndex: number) {
		return clsx(
			'flex aspect-square w-11 items-center justify-center rounded-xl bg-neutral-200 text-center text-lg font-semibold uppercase',
			{
				'bg-yellow-500': previousGuesses[wordIndex]?.[letterIndex]?.isInWord
			},
			{
				'bg-green-500': previousGuesses[wordIndex]?.[letterIndex]?.isInCorrectPosition
			}
		);
	}

	function renderWord(wordIndex: number, letterIndex: number) {
		return previousGuesses[wordIndex]?.[letterIndex]?.letter;
	}
</script>

<div class="flex flex-col items-center gap-8">
	<div class="flex flex-col">
		<p>Current attempt: {currentAttempt}</p>
		<p>Remaining attempts: {7 - currentAttempt}</p>
	</div>
	<div class="flex flex-col gap-2">
		{#each Array(6) as _, row}
			<div class="flex gap-2">
				{#each Array(5) as _, cell}
					<div class={getClasses(row, cell)}>
						{renderWord(row, cell)}
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
