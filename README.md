# Kryptografiverktøy i Svelte – Del 1 (Caesar)

Dette prosjektet er et nettbasert kryptografiverktøy bygget med Svelte som en del av oppgaven i driftsstøtte og programmering.

## Beskrivelse
Verktøyet lar brukeren utføre Caesar-kryptering og dekryptering av tekst basert på en numerisk nøkkel fra 0 til 25. Applikasjonen inneholder streng validering som sikrer at kun tillatte tegn (engelske bokstaver og mellomrom) behandles, og gir tydelige feilmeldinger ved feilaktig input.

## Implementert funksjonalitet
- **Caesar-kryptering** (Del 1 er fullført)
- **Caesar-dekryptering** (fungerer uavhengig av kryptering)
- Bevaring av store og små bokstaver, samt mellomrom
- Inputvalidering (blokkerer tall, spesialtegn, tomme felt og ugyldige nøkler)

## Slik starter du prosjektet
1. Åpne mappen i terminalen.
2. Installer avhengigheter:
   ```bash
   npm install