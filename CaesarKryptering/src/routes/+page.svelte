
<script lang="ts">
  let cipherType = $state("caesar");
  let inputText = $state("");
  let keyInput = $state("");
  let generatedKey = $state("");
  let resultText = $state("");
  let errorMessage = $state("");

  const alphabet = "ABCDEFGHIJKLMNOPQRSTUVWXYZ";

  // Validerer tekst: kun engelske bokstaver og vanlige mellomrom.
  function validateCommon(): boolean {
    errorMessage = "";

    if (!inputText.trim()) {
      errorMessage = "Feil: Teksten kan ikke være tom.";
      return false;
    }

    if (/[^a-zA-Z ]/.test(inputText)) {
      errorMessage =
        "Feil: Bruk kun engelske bokstaver og mellomrom. Tall og spesialtegn er ikke tillatt.";
      return false;
    }

    return true;
  }

  // Validerer Caesar-nøkkelen.
  function validateCaesar(): boolean {
    if (keyInput.trim() === "" || !/^\d+$/.test(keyInput.trim())) {
      errorMessage = "Feil: Caesar-nøkkelen må være et tall fra 0 til 25.";
      return false;
    }

    const key = Number(keyInput);

    if (!Number.isInteger(key) || key < 0 || key > 25) {
      errorMessage = "Feil: Caesar-nøkkelen må være et heltall fra 0 til 25.";
      return false;
    }

    return true;
  }

  // Validerer Vigenère-nøkkelen.
  function validateVigenereKey(): boolean {
    if (!/^[a-zA-Z]+$/.test(keyInput)) {
      errorMessage =
        "Feil: Vigenère-nøkkelen må inneholde bokstaver fra A til Z.";
      return false;
    }

    return true;
  }

  // Genererer en tilfeldig OTP-nøkkel.
  // Det lages én nøkkelbokstav for hver bokstav i teksten.
  // Mellomrom får ingen nøkkelbokstav.
  function generateOneTimePadKey(text: string): string {
    let key = "";

    for (const char of text) {
      if (char === " ") continue;

      const randomIndex = Math.floor(Math.random() * 26);
      key += alphabet[randomIndex];
    }

    return key;
  }

  // Validerer OTP-nøkkelen ved dekryptering.
  function validateOneTimePadKey(): boolean {
    if (!/^[a-zA-Z]+$/.test(keyInput)) {
      errorMessage =
        "Feil: OTP-nøkkelen må bare inneholde bokstaver fra A til Z.";
      return false;
    }

    const letterCount = inputText.replace(/ /g, "").length;

    if (keyInput.length !== letterCount) {
      errorMessage =
        `Feil: Nøkkelen må ha ${letterCount} bokstaver. ` +
        `Nøkkelen du skrev inn har ${keyInput.length} bokstaver.`;
      return false;
    }

    return true;
  }

  // Caesar-kryptering og dekryptering.
  function caesarCipher(
    text: string,
    key: string,
    decrypt = false
  ): string {
    let result = "";
    const numKey = Number(key);
    const shift = decrypt ? 26 - (numKey % 26) : numKey % 26;

    for (const char of text) {
      if (char === " ") {
        result += " ";
        continue;
      }

      const isUpperCase = char === char.toUpperCase();
      const index = alphabet.indexOf(char.toUpperCase());
      const newIndex = (index + shift) % 26;
      const transformedChar = alphabet[newIndex];

      result += isUpperCase
        ? transformedChar
        : transformedChar.toLowerCase();
    }

    return result;
  }

  // Vigenère-kryptering og dekryptering.
  // Nøkkelen gjentas, men mellomrom teller ikke med.
  function vigenereCipher(
    text: string,
    keyword: string,
    decrypt = false
  ): string {
    let result = "";
    const upperKeyword = keyword.toUpperCase();
    let keyIndex = 0;

    for (const char of text) {
      if (char === " ") {
        result += " ";
        continue;
      }

      const isUpperCase = char === char.toUpperCase();
      const charIndex = alphabet.indexOf(char.toUpperCase());
      const keyChar = upperKeyword[keyIndex % upperKeyword.length];
      const shift = alphabet.indexOf(keyChar);

      const newIndex = decrypt
        ? (charIndex - shift + 26) % 26
        : (charIndex + shift) % 26;

      const transformedChar = alphabet[newIndex];

      result += isUpperCase
        ? transformedChar
        : transformedChar.toLowerCase();

      keyIndex++;
    }

    return result;
  }

  // One-Time Pad-kryptering og dekryptering.
  // Nøkkelen gjentas IKKE og brukes én gang per bokstav.
  function oneTimePadCipher(
    text: string,
    key: string,
    decrypt = false
  ): string {
    let result = "";
    const upperKey = key.toUpperCase();
    let keyIndex = 0;

    for (const char of text) {
      if (char === " ") {
        result += " ";
        continue;
      }

      const isUpperCase = char === char.toUpperCase();
      const charIndex = alphabet.indexOf(char.toUpperCase());
      const keyValue = alphabet.indexOf(upperKey[keyIndex]);

      // A = 0, B = 1, ..., Z = 25.
      const newIndex = decrypt
        ? (charIndex - keyValue + 26) % 26
        : (charIndex + keyValue) % 26;

      const transformedChar = alphabet[newIndex];

      result += isUpperCase
        ? transformedChar
        : transformedChar.toLowerCase();

      keyIndex++;
    }

    return result;
  }

  // Krypterer med valgt metode.
  function handleEncrypt(): void {
    errorMessage = "";
    resultText = "";
    generatedKey = "";

    if (!validateCommon()) return;

    if (cipherType === "caesar") {
      if (!validateCaesar()) return;

      resultText = caesarCipher(inputText, keyInput, false);
    } else if (cipherType === "vigenere") {
      if (!validateVigenereKey()) return;

      resultText = vigenereCipher(inputText, keyInput, false);
    } else if (cipherType === "otp") {
      // OTP-nøkkelen genereres automatisk.
      generatedKey = generateOneTimePadKey(inputText);
      resultText = oneTimePadCipher(inputText, generatedKey, false);
    }
  }

  // Dekrypterer med valgt metode.
  function handleDecrypt(): void {
    errorMessage = "";
    resultText = "";
    generatedKey = "";

    if (!validateCommon()) return;

    if (cipherType === "caesar") {
      if (!validateCaesar()) return;

      resultText = caesarCipher(inputText, keyInput, true);
    } else if (cipherType === "vigenere") {
      if (!validateVigenereKey()) return;

      resultText = vigenereCipher(inputText, keyInput, true);
    } else if (cipherType === "otp") {
      if (!validateOneTimePadKey()) return;

      resultText = oneTimePadCipher(inputText, keyInput, true);
    }
  }

  // Nullstiller resultat og feilmeldinger når metoden endres.
  function handleMethodChange(): void {
    keyInput = "";
    generatedKey = "";
    resultText = "";
    errorMessage = "";
  }
</script>

<main class="container">
  <h1>Kryptografiverktøy - Del 1, 2 og 3</h1>

  <div class="form-group">
    <label for="cipherType">Velg metode:</label>
    <select
      id="cipherType"
      bind:value={cipherType}
      onchange={handleMethodChange}
    >
      <option value="caesar">Caesar-kryptering</option>
      <option value="vigenere">Vigenère-kryptering</option>
      <option value="otp">One-Time Pad (Vernam cipher)</option>
    </select>
  </div>

  <div class="form-group">
    <label for="inputText">Tekst:</label>
    <textarea
      id="inputText"
      bind:value={inputText}
      placeholder="Skriv inn tekst (kun engelske bokstaver og mellomrom)..."
      rows="4"
    ></textarea>
  </div>

  {#if cipherType !== "otp"}
    <div class="form-group">
      <label for="keyInput">
        {cipherType === "caesar"
          ? "Nøkkel (tall fra 0 til 25):"
          : "Nøkkel (bokstaver A-Z):"}
      </label>

      <input
        type={cipherType === "caesar" ? "number" : "text"}
        id="keyInput"
        bind:value={keyInput}
        placeholder={cipherType === "caesar" ? "F.eks. 3" : "F.eks. KEY"}
      />
    </div>
  {:else}
    <div class="info-box">
      <strong>One-Time Pad</strong>
    </div>
  {/if}

  {#if cipherType === "otp"}
    <div class="form-group">
      <label for="otpKeyInput">
        OTP-nøkkel ved dekryptering (A-Z):
      </label>
      <input
        type="text"
        id="otpKeyInput"
        bind:value={keyInput}
        placeholder="Lim inn den lagrede nøkkelen her"
      />
    </div>
  {/if}

  <div class="button-group">
    <button type="button" onclick={handleEncrypt}>Krypter</button>
    <button type="button" onclick={handleDecrypt}>Dekrypter</button>
  </div>

  {#if errorMessage}
    <div class="error-box" role="alert">
      <p>{errorMessage}</p>
    </div>
  {/if}

  {#if resultText}
    <div class="result-box">
      <h2>Resultat:</h2>
      <p class="result-text">{resultText}</p>
    </div>
  {/if}

  {#if generatedKey && cipherType === "otp"}
    <div class="result-box key-box">
      <h2>Generert OTP-nøkkel</h2>
      <p class="result-text">{generatedKey}</p>
      <p>
        Lagre nøkkelen. Du trenger den for å dekryptere teksten senere.
      </p>
      <button
        type="button"
        onclick={() => navigator.clipboard.writeText(generatedKey)}
      >
        Kopier nøkkel
      </button>
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
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
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

  textarea,
  input,
  select {
    padding: 0.75rem;
    font-size: 1rem;
    border: 1px solid #ccc;
    border-radius: 4px;
    outline: none;
    background: #fff;
    box-sizing: border-box;
    width: 100%;
  }

  textarea:focus,
  input:focus,
  select:focus {
    border-color: #0066cc;
  }

  .button-group {
    display: flex;
    flex-wrap: wrap;
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
    overflow-wrap: anywhere;
    color: #222;
  }

  .info-box {
    background: #f0f4f8;
    padding: 1rem;
    border-radius: 4px;
    margin-bottom: 1.2rem;
    color: #333;
  }

  .info-box p {
    margin-bottom: 0;
  }

  .key-box {
    background: #fff4d6;
    border-left-color: #c58b00;
  }

  .notice {
    margin-top: 1.5rem;
    font-size: 0.9rem;
    line-height: 1.5;
    color: #555;
  }

  small {
    margin-top: 0.4rem;
    color: #555;
  }

  @media (max-width: 640px) {
    .container {
      margin: 1rem;
      padding: 1.2rem;
    }
  }
</style>
