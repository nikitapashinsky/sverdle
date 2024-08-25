<script lang="ts">
	import { clsx } from 'clsx';

	let targetWord: string = $state('WORLD');
	let currentAttempt: number = $state(1);
	let currentGuess: string = $state('');
	let previousGuesses: object[] = $state([]);

	function createWordObj(
		guessedWord: string
	): { letter: string; isInWord: boolean; isInCorrectPosition: boolean }[] {
		return [...guessedWord].map((letter) => ({
			letter: letter.toUpperCase(),
			isInWord: false,
			isInCorrectPosition: false
		}));
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

	function handleGuess(
		guessedWord: { letter: string; isInWord: boolean; isInCorrectPosition: boolean }[],
		targetWord: string
	) {
		const checkedWord = checkWord(guessedWord, targetWord);
		previousGuesses = [...previousGuesses, checkedWord];
		console.log(previousGuesses?.[0]?.[0]?.isInWord);
	}

	function handleSubmit(event: KeyboardEvent) {
		if (event.key === 'Enter' && currentGuess.length === 5 && currentAttempt <= 6) {
			handleGuess(createWordObj(currentGuess), targetWord);
			currentGuess = '';
		}
	}

	function checkInput(event: KeyboardEvent) {
		if (!/^[a-zA-Z]$/.test(event.key)) {
			event.preventDefault();
		}
	}

	function getClasses(i, j) {
		return clsx(
			'flex aspect-square w-11 items-center justify-center rounded-xl bg-neutral-200 text-center text-lg font-semibold uppercase',
			{
				'bg-yellow-500': previousGuesses[i]?.[j]?.isInWord
			},
			{
				'bg-green-500':
					// prettier-ignore
					previousGuesses[i]?.[j]?.isInWord &&
					previousGuesses[i]?.[j]?.isInCorrectPosition
			}
		);
	}
</script>

<div class="flex flex-col items-center gap-8">
	<div class="flex flex-col gap-2">
		{#each Array(6) as _, i}
			<div class="flex gap-2">
				{#each Array(5) as _, j}
					<div class={getClasses(i, j)}>
						{#if i + 1 < currentAttempt}
							<!-- -->
						{:else if i === currentAttempt}
							<!-- -->
						{:else}
							<!-- Empty cell for future attempts -->
						{/if}
					</div>
				{/each}
			</div>
		{/each}
	</div>

	<!--
 clsx(
		'flex aspect-square w-11 items-center justify-center rounded-xl bg-neutral-200 text-center text-lg font-semibold uppercase',
		'bg-yellow-500': previousGuesses[i]?.[j]?.isInWord
	)
	-->

	<div class="flex flex-col items-center gap-2">
		<div class="flex gap-2">
			{#each ['Q', 'W', 'E', 'R', 'T', 'Y', 'U', 'I', 'O', 'P'] as letter}
				<div
					class={clsx(
						`flex rounded-lg bg-neutral-200 px-3 py-4 hover:bg-neutral-300`
						// {
						// 	'bg-neutral-300 text-neutral-500':
						// 		previousGuesses.join('').includes(letter) &&
						// 		!targetWord.includes(letter) &&
						// 		!previousGuesses[currentAttempt - 2].includes(letter)
						// },
						// {
						// 	'bg-green-600 text-white':
						// 		targetWord.includes(letter) &&
						// 		previousGuesses.length > 0 &&
						// 		previousGuesses[currentAttempt - 2].includes(letter)
						// },
						// {
						// 	'bg-yellow-600':
						// 		targetWord.includes(letter) &&
						// 		previousGuesses.length > 0 &&
						// 		previousGuesses[currentAttempt - 2].includes(letter) &&
						// 		targetWord.charAt(0) !== letter
						// }
					)}
				>
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
