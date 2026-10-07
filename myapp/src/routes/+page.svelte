<script>
	let message = $state('');
	let shift = $state('');
	let output = $state('');
	let error = $state('');

	function checkInput() {
		error = '';

		if (message.trim() === '') {
			error = 'Skriv inn en tekst.';
			return false;
		}

		if (!/^[A-Za-z ]+$/.test(message)) {
			error = 'Teksten kan bare inneholde bokstaver og mellomrom.';
			return false;
		}

		if (shift === '') {
			error = 'Skriv inn en nøkkel.';
			return false;
		}

		const number = Number(shift);

		if (!Number.isInteger(number) || number < 0 || number > 25) {
			error = 'Nøkkelen må være et heltall mellom 0 og 25.';
			return false;
		}

		return true;
	}

	function moveLetter(letter, amount) {
		if (letter === ' ') {
			return letter;
		}

		const code = letter.charCodeAt(0);
		let start;

		if (code >= 65 && code <= 90) {
			start = 65;
		} else {
			start = 97;
		}

		const position = code - start;
		const newPosition = (position + amount + 26) % 26;

		return String.fromCharCode(start + newPosition);
	}

	function encryptText() {
		if (!checkInput()) return;

		const amount = Number(shift);

		output = [...message]
			.map((letter) => moveLetter(letter, amount))
			.join('');
	}

	function decryptText() {
		if (!checkInput()) return;

		const amount = Number(shift);

		output = [...message]
			.map((letter) => moveLetter(letter, -amount))
			.join('');
	}

	function clearAll() {
		message = '';
		shift = '';
		output = '';
		error = '';
	}
</script>

<svelte:head>
	<title>Caesar Cipher</title>
</svelte:head>

<main>
	<h1>Caesar-kryptering</h1>

	<label for="message">Tekst</label>
	<textarea
		id="message"
		bind:value={message}
		placeholder="Skriv teksten her..."
	></textarea>

	<label for="shift">Nøkkel (0–25)</label>
	<input
		id="shift"
		type="number"
		min="0"
		max="25"
		bind:value={shift}
		placeholder="For eksempel 3"
	/>

	<div class="buttons">
		<button onclick={encryptText}>Krypter</button>
		<button onclick={decryptText}>Dekrypter</button>
		<button onclick={clearAll}>Tøm</button>
	</div>

	{#if error}
		<p class="error">{error}</p>
	{/if}

	{#if output}
		<section>
			<h2>Resultat</h2>
			<div class="output">{output}</div>
		</section>
	{/if}
</main>

<style>
	main {
		max-width: 650px;
		margin: 40px auto;
		padding: 25px;
		font-family: Arial, sans-serif;
	}

	h1 {
		margin-bottom: 30px;
	}

	label {
		display: block;
		margin: 15px 0 7px;
		font-weight: bold;
	}

	textarea {
		width: 100%;
		height: 140px;
		padding: 10px;
		box-sizing: border-box;
		resize: vertical;
	}

	input {
		width: 180px;
		padding: 9px;
		box-sizing: border-box;
	}

	.buttons {
		display: flex;
		gap: 10px;
		margin-top: 20px;
	}

	button {
		padding: 10px 18px;
		cursor: pointer;
	}

	.error {
		margin-top: 20px;
		padding: 10px;
		border: 1px solid #d00;
	}

	section {
		margin-top: 25px;
	}

	.output {
		padding: 15px;
		border: 1px solid #aaa;
		word-break: break-word;
	}
</style>