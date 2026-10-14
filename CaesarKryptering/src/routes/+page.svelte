<script lang="ts">
  let inputText = $state("");
  let keyInput = $state("");
  let resultText = $state("");
  let errorMessage = $state("");

  const alphabet = "ABCDEFGHIJKLMNOPQRSTUVWXYZ";

  // Validering av brukerens input i henhold til oppgavespesifikasjonene
  function validateInput(): boolean {
    errorMessage = "";

    // Sjekk om teksten er tom
    if (!inputText.trim()) {
      errorMessage = "Feil: Teksten kan ikke være tom.";
      return false;
    }

    // Sjekk om nøkkelen mangler eller ikke er et tall
    if (keyInput === "" || isNaN(Number(keyInput))) {
      errorMessage = "Feil: Nøkkelen må være et tall.";
      return false;
    }

    const key = Number(keyInput);

    // Sjekk om nøkkelen er et heltall innenfor tillatt intervall (0-25)
    if (!Number.isInteger(key) || key < 0 || key > 25) {
      errorMessage = "Feil: Nøkkelen må være et heltall fra 0 til 25.";
      return false;
    }

    // Sjekk for ulovlige tegn (kun engelske bokstaver og mellomrom er tillatt)
    const invalidCharRegex = /[^a-zA-Z\s]/;
    if (invalidCharRegex.test(inputText)) {
      errorMessage = "Feil: Teksten kan kun inneholde engelske bokstaver og mellomrom (ingen tall eller spesialtegn tillatt).";
      return false;
    }

    return true;
  }

  // Hovedfunksjon for Caesar-kryptering og dekryptering
  function caesarCipher(text: string, key: string | number, decrypt = false): string {
    let result = "";
    const numKey = Number(key);
    
    // Ved dekryptering snur vi forskyvningen motsatt vei. 
    // Vi legger til 26 for å unngå negative tall før modulo-operasjonen.
    const shift = decrypt ? (26 - (numKey % 26)) : (numKey % 26);

    for (let i = 0; i < text.length; i++) {
      let char = text[i];

      // Mellomrom skal beholdes på samme sted og ikke krypteres
      if (char === " ") {
        result += " ";
        continue;
      }

      // Bevarer opprinnelig skrivemåte (store eller små bokstaver)
      let isUpperCase = char === char.toUpperCase();
      let upperChar = char.toUpperCase();
      let index = alphabet.indexOf(upperChar);

      if (index !== -1) {
        // Beregner ny posisjon i alfabetet ved hjelp av modulo 26 for sirkulær forskyvning
        let newIndex = (index + shift) % 26;
        let transformedChar = alphabet[newIndex];
        
        // Returnerer bokstaven i riktig register (store/små)
        result += isUpperCase ? transformedChar : transformedChar.toLowerCase();
      } else {
        result += char;
      }
    }

    return result;
  }

  // Kjører kryptering hvis valideringen lykkes
  function handleEncrypt() {
    if (!validateInput()) return;
    resultText = caesarCipher(inputText, keyInput, false);
  }

  // Kjører dekryptering uavhengig av om teksten ble kryptert først
  function handleDecrypt() {
    if (!validateInput()) return;
    resultText = caesarCipher(inputText, keyInput, true);
  }
</script>

<main class="container">
  <h1>Kryptografiverktøy - Del 1: Caesar</h1>

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
    <label for="keyInput">Nøkkel (0 - 25):</label>
    <input 
      type="number" 
      id="keyInput" 
      bind:value={keyInput} 
      min="0" 
      max="25" 
      placeholder="F.eks. 3"
    />
  </div>

  <div class="button-group">
    <button onclick={handleEncrypt}>Krypter</button>
    <button onclick={handleDecrypt}>Dekrypter</button>
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

  textarea, input {
    padding: 0.75rem;
    font-size: 1rem;
    border: 1px solid #ccc;
    border-radius: 4px;
    outline: none;
  }

  textarea:focus, input:focus {
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