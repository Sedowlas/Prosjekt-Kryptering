<script lang="ts">
let cipherType = $state("");
let inputText = $state("");
let keyInput = $state("");
let resultText = $state("");
let errorMessage = $state("");

const alphabet = "ABCDEFGHIJKLMNOPQRSTUVWXYZ";

// Validering for Caesar-metoden (tall fra 0 til 25)
  function validateCaesar() {
    if (keyInput === "" || isNaN(Number(keyInput))) {
      errorMessage = "Feil: Caesar-nøkkelen må være et tall.";
      return false;
    }

    const key = Number(keyInput);
    if (!Number.isInteger(key) || key < 0 || key > 25) {
      errorMessage = "Feil: Caesar-nøkkelen må være et heltall fra 0 til 25.";
      return false;
    }
    return true;
  }

  // Validering for Vigenère-metoden (kun bokstaver fra A til Z)
  function validateVigenereKey() {
    if (!keyInput.trim()) {
      errorMessage = "Feil: Vigenère-nøkkelen kan ikke være tom.";
      return false;
    }

    // Sjekk at nøkkelen kun inneholder engelske bokstaver (ingen tall, mellomrom eller spesialtegn)
    const keyRegex = /^[a-zA-Z]+$/;
    if (!keyRegex.test(keyInput)) {
      errorMessage = "Feil: Vigenère-nøkkelen kan kun inneholde bokstaver (A-Z), ingen tall, mellomrom eller spesialtegn.";
      return false;
    }
    return true;
  }

  // Felles inputvalidering for tekst
  function validateCommon() {
    errorMessage = "";

    if (!inputText.trim()) {
      errorMessage = "Feil: Teksten kan ikke være tom.";
      return false;
    }

    const invalidCharRegex = /[^a-zA-Z\s]/;
    if (invalidCharRegex.test(inputText)) {
      errorMessage = "Feil: Teksten kan kun inneholde engelske bokstaver og mellomrom (ingen tall eller spesialtegn tillatt).";
      return false;
    }

    return true;
  }

  // Caesar-funksjon fra Del 1
  function caesarCipher(text: string | any[], key: string, decrypt = false) {
    let result = "";
    const numKey = Number(key);
    const shift = decrypt ? (26 - (numKey % 26)) : (numKey % 26);

    for (let i = 0; i < text.length; i++) {
      let char = text[i];

      if (char === " ") {
        result += " ";
        continue;
      }

      let isUpperCase = char === char.toUpperCase();
      let upperChar = char.toUpperCase();
      let index = alphabet.indexOf(upperChar);

      if (index !== -1) {
        let newIndex = (index + shift) % 26;
        let transformedChar = alphabet[newIndex];
        result += isUpperCase ? transformedChar : transformedChar.toLowerCase();
      } else {
        result += char;
      }
    }

    return result;
  }

  // Vigenère-funksjon for kryptering og dekryptering
  function vigenereCipher(text: string | any[], keyword: string, decrypt = false) {
    let result = "";
    const upperKeyword = keyword.toUpperCase();
    let keyIndex = 0; // Holder styr på posisjonen i nøkkelen

    for (let i = 0; i < text.length; i++) {
      let char = text[i];

      // Mellomrom beholdes, og vi teller IKKE opp nøkkelindeksen for mellomrom
      if (char === " ") {
        result += " ";
        continue;
      }

      let isUpperCase = char === char.toUpperCase();
      let upperChar = char.toUpperCase();
      let charPos = alphabet.indexOf(upperChar);

      if (charPos !== -1) {
        // Henter den aktuelle bokstaven fra nøkkelen (repeteres ved hjelp av modulo)
        let keyChar = upperKeyword[keyIndex % upperKeyword.length];
        let shift = alphabet.indexOf(keyChar);

        // Hvis dekryptering, snur vi forskyvningen motsatt vei
        let newIndex;
        if (decrypt) {
          newIndex = (charPos - shift + 26) % 26;
        } else {
          newIndex = (charPos + shift) % 26;
        }

        let transformedChar = alphabet[newIndex];
        result += isUpperCase ? transformedChar : transformedChar.toLowerCase();

        // Gå til neste bokstav i nøkkelen ettersom vi har behandlet en gyldig bokstav
        keyIndex++;
      } else {
        result += char;
      }
    }

    return result;
  }

  // Hovedfunksjoner for knapper
  function handleEncrypt() {
    if (!validateCommon()) return;

    if (cipherType === "caesar") {
      if (!validateCaesar()) return;
      resultText = caesarCipher(inputText, keyInput, false);
    } else {
      if (!validateVigenereKey()) return;
      resultText = vigenereCipher(inputText, keyInput, false);
    }
  }

  function handleDecrypt() {
    if (!validateCommon()) return;

    if (cipherType === "caesar") {
      if (!validateCaesar()) return;
      resultText = caesarCipher(inputText, keyInput, true);
    } else {
      if (!validateVigenereKey()) return;
      resultText = vigenereCipher(inputText, keyInput, true);
    }
  }
</script>

<main class="container">
  <h1>Kryptografiverktøy - Del 1 & Del 2</h1>

  <!-- Valg av kryptografimetode -->
  <div class="form-group">
    <label for="cipherType">Velg metode:</label>
    <select id="cipherType" bind:value={cipherType}>
      <option value="caesar">Caesar-kryptering</option>
      <option value="vigenere">Vigenère-kryptering</option>
    </select>
  </div>

  <div class="form-group">
    <label for="inputText">Tekst:</label>
    <textarea 
      id="inputText" 
      bind:value={inputText} 
      placeholder="Skriv inn tekst her (kun engelske bokstaver og mellomrom)..."
      rows="4"
    ></textarea>
  </div>

  <div class="form-group">
    <label for="keyInput">
      {#if cipherType === 'caesar'}
        Nøkkel (tall fra 0 til 25):
      {:else}
        Nøkkel (ord/bokstaver A-Z):
      {/if}
    </label>
    <input 
      type={cipherType === 'caesar' ? 'number' : 'text'} 
      id="keyInput" 
      bind:value={keyInput} 
      placeholder={cipherType === 'caesar' ? 'F.eks. 3' : 'F.eks. KEY'}
    />
  </div>

  <div class="button-group">
    <!-- svelte-ignore event_directive_deprecated -->
    <button on:click={handleEncrypt}>Krypter</button>
    <!-- svelte-ignore event_directive_deprecated -->
    <button on:click={handleDecrypt}>Dekrypter</button>
  </div>

  {#if errorMessage}
    <div class="error-box">
      <p>{errorMessage}</p>
    </div>
  {/if}

  {#if resultText}
    <div class="result-box">
      <h2>Resultat:</h2>
      <p class="result-text">{resultText}</p>
    </div>
  {/if}
</main>

<style>
  .container {
    max-width: 600px;
    margin: 3rem auto;
    font-family: Arial, sans-serif;
    padding: 2rem;
    background: #f9f9f9;
    border-radius: 8px;
    box-shadow: 0 4px 12px rgba(0,0,0,0.1);
  }

  h1 {
    font-size: 1.5rem;
    margin-bottom: 1.5rem;
    color: #333;
  }

  .form-group {
    margin-bottom: 1.2rem;
    display: flex;
    flex-direction: column;
  }

  label {
    font-weight: bold;
    margin-bottom: 0.4rem;
    color: #444;
  }

  textarea, input, select {
    padding: 0.75rem;
    font-size: 1rem;
    border: 1px solid #ccc;
    border-radius: 4px;
    outline: none;
    background: #fff;
  }

  textarea:focus, input:focus, select:focus {
    border-color: #0066cc;
  }

  .button-group {
    display: flex;
    gap: 1rem;
    margin-bottom: 1.5rem;
  }

  button {
    padding: 0.75rem 1.5rem;
    font-size: 1rem;
    font-weight: bold;
    color: #fff;
    background-color: #0066cc;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    transition: background-color 0.2s;
  }

  button:hover {
    background-color: #004d99;
  }

  .error-box {
    background-color: #ffe6e6;
    border-left: 4px solid #cc0000;
    padding: 0.75rem;
    margin-bottom: 1rem;
    color: #cc0000;
    font-weight: bold;
    border-radius: 0 4px 4px 0;
  }

  .result-box {
    margin-top: 1.5rem;
    background: #e6f2ff;
    border-left: 4px solid #0066cc;
    padding: 1rem;
    border-radius: 0 4px 4px 0;
  }

  .result-box h2 {
    font-size: 1.1rem;
    margin-bottom: 0.5rem;
    color: #004d99;
  }

  .result-text {
    font-size: 1.2rem;
    word-break: break-all;
    color: #222;
  }
</style>