# Kryptografiverktøy i Svelte – Del 1 og Del 2

Dette prosjektet er et nettbasert kryptografiverktøy bygget med Svelte.

## Beskrivelse av implementert funksjonalitet

- **Del 1 (Caesar):** Kryptering og dekryptering ved hjelp av Caesar-chifferet med tallnøkkel (0-25).
- **Del 2 (Vigenère):** Kryptering og dekryptering ved hjelp av Vigenère-chifferet basert på en tekstnøkkel (A-Z). Nøkkelen gjentas automatisk ved behov, og mellomrom behandles korrekt uten å forbruke nøkkelbokstaver.
- **Validering:** Streng kontroll av både tekst og nøkkel for begge metoder, med tydelige feilmeldinger ved ulovlige tegn (som tall eller spesialtegn).

## Slik starter du prosjektet
1. Åpne mappen i terminalen.
2. Installer avhengigheter:
   ```bash
   npm install