# Lektion 0: Installation och första sessionen

**Längd:** 4 timmar. 45 min genomgång, 2 h 30 min labb, 30 min quiz och reflektion, 15 min buffert.

**Förkunskap**
- Ett konto som ger tillgång till Claude Code: Pro, Max, Ultra, Team, Enterprise, Claude Console, eller en molnleverantör som stöds.
- Windows med PowerShell.
- Git installerat. Git for Windows rekommenderas (se steg 1).
- Ett .NET-repo som du kan läsa i men inte behöver ändra permanent.

## Mål

Efter lektionen kan du:

1. Installera Claude Code på Windows och verifiera installationen.
2. Starta en session i ett projekt och logga in.
3. Ställa frågor om en kodbas och kontrollera svaren mot koden.
4. Använda grundkommandona `/help`, `/context`, `/exit` och `claude -c`.
5. Växla permission mode med `Shift+Tab` och förklara vad plan-läge gör.
6. Köra Claude i skriptläge med `claude -p`.

## Teori (genomgång, 45 min)

### Vad Claude Code är
Claude Code är ett kommandoradsverktyg som läser ditt projekt, svarar på frågor och kan föreslå eller göra ändringar i filer. Det körs i en terminal, i din projektmapp.
*Källa: [Quickstart](https://code.claude.com/docs/en/quickstart).*

### Installation
På Windows installeras det med PowerShell:
```powershell
irm https://claude.ai/install.ps1 | iex
```
Efter installationen öppnar du ett nytt terminalfönster och kontrollerar med `claude --version`.
*Källa: Quickstart, steg 1.*

Git for Windows rekommenderas, så att Claude Code kan använda Bash-verktyget. Om det inte är installerat används PowerShell som skalverktyg. WSL behöver inte Git for Windows.
*Källa: Quickstart.*

### Första prompten
Claude läser projektfiler efter behov. Du behöver inte lägga till filer manuellt för att få svar om kodbasen. Ställ frågan direkt.
*Källa: Quickstart, steg 3, och [Best practices](https://code.claude.com/docs/en/best-practices).*

### Permission modes
`Shift+Tab` växlar permission mode i den aktuella sessionen. I plan-läge läser Claude filer och föreslår en plan, men ändrar inga filer förrän du godkänt planen. Statusraden visar när plan-läget är aktivt.
*Källa: [Common workflows](https://code.claude.com/docs/en/common-workflows), avsnittet "Plan before editing", och Quickstart.*

### Kontext
Allt Claude vet i en session är kontext: dina instruktioner, filer som lästs, Claudes egna svar. `/context` visar vad som laddats. Det är första stället att titta när något verkar konstigt.
*Källa: [Explore the context window](https://code.claude.com/docs/en/context-window).*

### Skriptläge
`claude -p "fråga"` kör en fråga och avslutar. Det är samma modell, men utan interaktiv session. Det används i skript och i CI.
*Källa: [CLI reference](https://code.claude.com/docs/en/cli-reference) och Quickstart.*

## Grundkommandon i den här lektionen

| Kommando | Vad det gör |
| --- | --- |
| `claude --version` | Visar installerad version. |
| `claude` | Startar en interaktiv session i aktuell mapp. |
| `claude "fråga"` | Startar en interaktiv session med en första prompt. |
| `claude -p "fråga"` | Kör en fråga och avslutar. |
| `claude -c` | Fortsätter den senaste konversationen i mappen. |
| `/help` | Visar tillgängliga kommandon. |
| `/context` | Visar vad som ligger i kontextfönstret. |
| `/exit` | Avslutar sessionen. |
| `Shift+Tab` | Växlar permission mode. |

## Labb (2 h 30 min)

### Steg 1: installera (15 min)
Öppna PowerShell och kör:
```powershell
irm https://claude.ai/install.ps1 | iex
```
Öppna ett nytt terminalfönster och kör:
```bash
claude --version
```
**Förväntat:** ett versionsnummer följt av `(Claude Code)`.
**Om `claude` inte hittas:** installationsmappen ligger inte på PATH. Se felsökningen i [Quickstart](https://code.claude.com/docs/en/quickstart).

### Steg 2: första sessionen (20 min)
Gå till ditt .NET-repo och starta:
```bash
cd C:\Repos\ditt-repo
claude
```
Logga in när du uppmanas. Skriv `/help` och läs igenom listan.
**Förväntat:** en lista över kommandon och en prompt där du kan skriva.

### Steg 3: ställ tre frågor (30 min)
Ställ dessa frågor, en i taget:
- `what does this project do?`
- `where is the main entry point?`
- `explain the folder structure`

Kontrollera varje svar mot koden själv. Anteckna en fråga där svaret var fel, ofullständigt eller gissat.
**Förväntat:** tre svar, där minst två stämmer med koden. Skillnaderna är lektionens observation.

### Steg 4: se vad som laddats (20 min)
Kör:
```text
/context
```
Anteckna tre saker som står i listan och vad du tror att de gör. Du använder listan i lektion 1.
**Förväntat:** en lista över kontextens delar.

### Steg 5: plan-läge (20 min)
1. Tryck `Shift+Tab` tills statusraden visar plan-läge.
2. Ställ frågan: `what would you change to add logging to the main service?`
3. Kontrollera att inga filer har ändrats. Använd `git status` i ett annat fönster om du är osäker.
4. Tryck `Shift+Tab` för att gå tillbaka till normalläge.

**Förväntat:** en plan i svaret och inga ändrade filer.

### Steg 6: återuppta sessionen (20 min)
1. Skriv `/exit`.
2. Starta om med:
```bash
claude -c
```
**Förväntat:** den föregående konversationen finns kvar i sessionen.

### Steg 7: skriptläge (30 min)
Kör utanför sessionen, i PowerShell:
```bash
claude -p "what does this project do? answer in three sentences"
```
Jämför svaret med svaret i steg 3. Skriv ned två skillnader mellan `claude` och `claude -p`.
**Förväntat:** ett svar som skrivs ut och att programmet avslutas av sig självt.

### Steg 8: en riktig ändring i en säker kopia (30 min)
Gör det i en kopia av repot eller på en separat gren, så att din huvudkod inte påverkas.
1. Kontrollera att arbetskatalogen är ren: `git status`.
2. Starta Claude och be om en liten ändring: `add a one-line comment to the main file`.
3. Granska diffen innan du godkänner. Stoppa med `Esc` om något ser fel ut.
4. Kasta ändringen när du är klar: `git checkout -- .`
**Förväntat:** en godkänd ändring som du kan se i `git diff`, och en ren arbetskatalog efter steg 4.

## Quiz (30 min)

1. Vilket kommando visar installerad version av Claude Code?
2. Vad gör `claude -c`?
3. Vad är skillnaden mellan `claude "fråga"` och `claude -p "fråga"`?
4. Varför rekommenderas Git for Windows?
5. Vad händer när du trycker `Shift+Tab` i en session?

Facit finns i [quiz-facit.md](quiz-facit.md), som bara instruktören ska använda.

## Referenser

- [Quickstart](https://code.claude.com/docs/en/quickstart): installation, första sessionen, grundkommandon.
- [CLI reference](https://code.claude.com/docs/en/cli-reference): alla flaggor och kommandon.
- [Commands](https://code.claude.com/docs/en/commands): slash-kommandon.
- [Common workflows](https://code.claude.com/docs/en/common-workflows): plan-läge och återupptagning.
- [Explore the context window](https://code.claude.com/docs/en/context-window): vad som laddas och när.

## Slide-outline (8 slides)

1. **Titel:** "Lektion 0: Installation och första sessionen".
2. **Mål:** de sex punkterna från avsnittet Mål.
3. **Vad Claude Code gör i ditt repo:** en bild av terminalen och projektmappen.
4. **Installation och verifiering:** installationskommandot och `claude --version` i kodblock.
5. **Första sessionen:** `claude`, inloggning, `/help`.
6. **Permission modes:** `Shift+Tab` med tre lägen i en bild.
7. **Labb i korthet:** de åtta stegen med förväntat resultat.
8. **Innan du går vidare:** de tre frågor eleven ska kunna svara på, och bedömningens checklista.

## Bedömning

Lektionen är godkänd när eleven:

- [ ] kör `claude --version` och kan förklara vad svaret betyder,
- [ ] har ställt de tre frågorna och kontrollerat svaren mot koden,
- [ ] kan förklara skillnaden mellan `claude` och `claude -p`,
- [ ] har växlat till plan-läge och verifierat att inga filer ändrades,
- [ ] har återupptagit en session med `claude -c`,
- [ ] klarar minst 4 av 5 quizfrågor.

## Vanliga fel

- **`claude` hittas inte efter installation:** öppna ett nytt terminalfönster. Om det inte hjälper, kontrollera PATH. Se Quickstart.
- **`The token '&&' is not a valid statement separator`:** du kör CMD-kommandon i PowerShell. Använd PowerShell-kommandot i labbet.
- **Ingen konversation hittas med `claude -c`:** du startade i en annan mapp. `claude -c` läser den senaste konversationen i den aktuella mappen.
- **Svaret är fel men låter säkert:** be Claude visa filen eller raden den byggde svaret på. Det är ett bättre test än att läsa svaret.
