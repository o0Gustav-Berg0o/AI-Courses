# Lektion 5: Headless och automation

**Längd:** 4 timmar.
- Genomgång (läs och diskutera): 2 timmar.
- Labb: 1 timme 35 minuter.
- Quiz och reflektion: 25 minuter.

**Förkunskap**
- Lektion 0 till 4 är genomförda.
- Ett .NET-repo med ett test-projekt.
- Kunskap om att köra kommandon i en terminal och läsa exit codes.

## Mål

Efter lektionen kan du:

1. Köra Claude utan person i loopen med `claude -p`, och förklara vad som saknas när ingen godkänner.
2. Välja utdataformat (`text`, `json`, `stream-json`) och läsa resultatet i ett skript.
3. Begränsa vad Claude får göra med `--allowedTools` och `--permission-mode`, och förklara skillnaden.
4. Förklara varför `--bare` är rekommenderat för skript och CI.
5. Förklara vilka risker som finns med en körning utan person, och hur du minskar dem.

---

## Teori (genomgång, 2 timmar)

Varje avsnitt har en källa. Där något är en rekommendation står det uttryckligen.

### 1. Vad headless betyder

`claude -p` kör en prompt, skriver ut svaret och avslutas. Det finns ingen interaktiv session, och ingen person som godkänner varje steg. Det är samma verktyg, men det används i skript och CI.

Dokumentationen kallar det "Run Claude Code programmatically". Den säger också att Claude Code avslutas med exit code 0 vid lyckad körning och ett annat exit code vid fel, så att skript kan läsa resultatet.

*Källa: [Run Claude Code programmatically](https://code.claude.com/docs/en/headless), avsnittet "Basic usage".*

### 2. Det som saknas utan person

Det viktigaste i den här lektionen är vad som inte finns när ingen godkänner. I en interaktiv session frågar Claude när något behöver godkännas. I `-p` finns ingen som svarar.

Dokumentationen beskriver följden för verktyg som kräver godkännande: i ett `claude -p`-anrop i manuellt läge nekas ett anrop som behöver godkännande, om inget annat godkänner det, till exempel en `PermissionRequest`-hook eller `--permission-prompt-tool`. Från och med v2.1.302 startar `-p`-körningar i manuellt läge när inget annat sätter ett läge.

*Källa: [Run Claude Code programmatically](https://code.claude.com/docs/en/headless), avsnittet "Auto-approve tools". Versionsnumret står i samma avsnitt.*

### 3. Utdataformat

- **`text`** (standard): ren text.
- **`json`**: ett JSON-objekt med resultat, session-ID och metadata. Svarets text finns i fältet `result`.
- **`stream-json`**: en JSON-rad per händelse, i realtid.

Med `json` får du också `total_cost_usd`, vilket gör att skriptet kan spåra kostnaden. Det är en klientuppskattning, och kan skilja sig från fakturan.

```bash
claude -p "Summarize this project" --output-format json | jq -r '.result'
```

*Källa: [Run Claude Code programmatically](https://code.claude.com/docs/en/headless), avsnitten "Get structured output" och "Pipe data through Claude".*

### 4. Tillåt verktyg med `--allowedTools`

`--allowedTools` låter Claude använda vissa verktyg utan att fråga. Listar du `Read` och `Edit` får Claude läsa och ändra filer utan godkännande. Listar du `Bash` får den köra skalkommandon, utom i en körning som startar i auto mode, där ett bart `Bash` tas bort som bred allow-regel.

Du kan också begränsa till specifika kommandon med regelsyntax: `Bash(git diff *)` tillåter kommandon som börjar med `git diff`. Mellanslaget före `*` spelar roll. Utan det matchar `Bash(git diff*)` även `git diff-index`.

Verktyg som du inte listat körs bara om de inte kräver godkännande. Kravet gäller alltså både nekande och tillåtande.

*Källa: [Run Claude Code programmatically](https://code.claude.com/docs/en/headless), avsnitten "Auto-approve tools" och "Create a commit".*

### 5. Lägen för körningen

Med `--permission-mode` sätter du en grundnivå för hela körningen. De vanliga är:

- **`auto`**: en klassificerare granskar de flesta åtgärder i stället för dig.
- **`dontAsk`**: allt som skulle fråga nekas. Användbart för låsta CI-körningar.
- **`acceptEdits`**: Claude skriver filer utan att fråga, och vanliga filkommandon i arbetskatalogen godkänns.

Med `--permission-prompts none` säger du att ingen finns som kan svara på prompts. Det som skulle fråga nekas, och Claude får veta att den inte ska försöka igen. Flaggan kräver v2.1.259 eller senare.

Du kan också sätta `permissions.denyInsteadOfPrompting` till `true` i användar- eller managed-inställningar. Då gäller det i alla körningar, inte bara den enskilda.

*Källa: [Run Claude Code programmatically](https://code.claude.com/docs/en/headless), avsnitten "Customize the system prompt", "Auto-approve tools" och "Turn off permission prompts in unattended runs".*

### 6. `--bare` för skript och CI

Med `--bare` hoppar `claude -p` över automatisk inläsning av hooks, skills, subagenter, plugins, MCP-servrar, auto memory och CLAUDE.md. Resultatet blir detsamma på varje maskin, och det är därför dokumentationen rekommenderar det för skript och SDK-anrop.

Det finns en konsekvens: en hook i någons `~/.claude` eller en MCP-server i projektets `.mcp.json` körs inte. Du lägger då till det du behöver med flaggor, till exempel `--settings` och `--mcp-config`.

Med `--bare` använder Claude API-nyckeln i `ANTHROPIC_API_KEY`, och inte din inloggning i abonnemanget. Dokumentationen säger också att flaggan kommer att bli standard för `-p` i en framtida version.

*Källa: [Run Claude Code programmatically](https://code.claude.com/docs/en/headless), avsnittet "Start faster with bare mode" och notisen om att den blir standard.*

### 7. Risken i `-p` utan `--bare`

Utan `--bare` kör en `-p`-session de hooks som finns i projektets `.claude/settings.json` och de MCP-servrar som finns i `.mcp.json`, även i en mapp du aldrig litat på. Det visas ingen förtroendedialog och inget godkännande per server.

Det här är en säkerhetsfråga. Om du kör `claude -p` i ett repo som du klonat från någon annan, kan dess hooks köras. Använd `--bare` i CI, och granska hooks och `.mcp.json` innan du kör lokalt i ett okänt repo.

*Källa: [Run Claude Code programmatically](https://code.claude.com/docs/en/headless), avsnittet "Start faster with bare mode", stycket om att `-p` kör hooks och MCP-servrar utan förtroendedialog.*

### 8. Fortsätt en konversation

`--continue` fortsätter den senaste konversationen i mappen. `--resume` med ett session-ID fortsätter en specifik. Du kan fånga session-ID med `jq`:

```bash
session_id=$(claude -p "Start a review" --output-format json | jq -r '.session_id')
claude -p "Continue that review" --resume "$session_id"
```

*Källa: [Run Claude Code programmatically](https://code.claude.com/docs/en/headless), avsnittet "Continue conversations".*

### 9. Stdin och storleksgräns

Du kan pipa in innehåll: `cat build-error.txt | claude -p "concisely explain the root cause"`. Stdin är begränsad till 10 MB. Större innehåll skriver du till en fil och refererar till sökvägen i prompten.

*Källa: [Run Claude Code programmatically](https://code.claude.com/docs/en/headless), avsnittet "Pipe data through Claude".*

### 10. Kostnad och gränser

Två flaggor begränsar körningar:

- **`--max-budget-usd`** sätter en kostnadsgräns. Dokumentationen säger att körningen stoppar kvarvarande bakgrundsarbete när gränsen nås.
- **`--max-turns`** begränsar antalet agentiska turer i print-läge. Standard är ingen gräns.

Kom ihåg att `total_cost_usd` är en uppskattning. Din faktiska kostnad syns i `/usage` och på kontot.

*Källa: [Run Claude Code programmatically](https://code.claude.com/docs/en/headless), avsnittet "Background tasks at exit" (för `--max-budget-usd`). `--max-turns` står i [CLI reference](https://code.claude.com/docs/en/cli-reference).*

### 11. Schemalagda körningar

När ingen session är öppen behöver något annat starta körningen. Dokumentationen listar fyra alternativ:

- **Cloud scheduled tasks**: körs i molnet, även när din dator är av. Konfigureras på claude.ai.
- **Desktop scheduled tasks**: körs på din dator, via desktop-appen. Bra för lokala filer och ändringar som inte är committade.
- **GitHub Actions**: körs i din CI, och kan kopplas till repohändelser.
- **`/loop`**: körs i den aktuella CLI-sessionen, med ett tidsintervall. Det kräver att sessionen är öppen.

Dokumentationen säger också att en schemalagd uppgift inte kan ställa följdfrågor. Prompten måste därför säga vad framgång är och vad som ska göras med resultatet.

*Källa: [Common workflows](https://code.claude.com/docs/en/common-workflows), avsnittet "Run Claude on a schedule". `/loop` och `/schedule` finns i [Commands](https://code.claude.com/docs/en/commands). Att `/schedule` heter "routines" i äldre dokumentation står i samma tabell.*

### 12. Checklista för en säker körning utan person

Det här är kursens rekommendation, byggd på dokumentationens delar:

1. **`--bare`** i CI, så att inget i projektet körs oväntat.
2. **Smalt `--allowedTools`**, till exempel `Bash(dotnet test *)`, i stället för bart `Bash`.
3. **`--permission-prompts none`** eller `denyInsteadOfPrompting`, så att inget väntar på en person.
4. **`--max-budget-usd`** som tak för kostnaden.
5. **`--output-format json`** och en kontroll av exit code, så att skriptet reagerar på fel.
6. **En tydlig prompt** som säger vad framgång är och vad som ska göras med resultatet.

*Källa: Kursens rekommendation. Varje punkt har sin källa i avsnitten 4 till 11.*

### Sammanfattning av genomgången

- `claude -p` kör utan person. Det som kräver godkännande nekas, om inget annat godkänner.
- Välj utdataformat, och läs `result`, `session_id` och `total_cost_usd` i `json`.
- Begränsa med smala `--allowedTools` och ett lämpligt `--permission-mode`.
- Använd `--bare` i CI, och granska hooks och `.mcp.json` i okända repon.
- Sätt kostnadsgränser, och kontrollera exit code.
- Schemalagda körningar kräver att prompten säger vad framgång är.

---

## Grundkommandon i den här lektionen

| Kommando | Vad det gör |
| --- | --- |
| `claude -p "fråga"` | Kör en prompt, skriver ut svaret och avslutas. |
| `claude -p ... --output-format json` | Returnerar JSON med `result`, `session_id` och `total_cost_usd`. |
| `claude -p ... --output-format stream-json --verbose` | Strömmar händelser som JSON-rader. |
| `--allowedTools "Read,Edit,Bash(git diff *)"` | Tillåter angivna verktyg utan att fråga. |
| `--permission-mode auto` | Låter en klassificerare granska åtgärderna. |
| `--permission-prompts none` | Nekar i stället för att fråga. |
| `--bare` | Hoppar över automatisk inläsning av hooks, MCP, CLAUDE.md m.m. |
| `--continue` / `--resume <id>` | Fortsätter senaste eller en specifik konversation. |
| `--max-budget-usd <belopp>` | Sätter en kostnadsgräns för körningen. |

---

## Labb (1 timme 35 minuter)

Labbet görs i ett .NET-repo med ett test-projekt. Steg 3 och 5 kan ändra filer, så gör dem i en kopia eller på en separat gren.

### Steg 1: första headless-körningen (10 min)
1. Kör från PowerShell utanför en session: `claude -p "what does this project do? answer in three sentences"`.
2. Jämför svaret med det du fick i lektion 0.
3. Kontrollera exit code direkt efter: `echo $LASTEXITCODE`.

**Förväntat:** ett svar, och exit code `0`. Anteckna en skillnad mot en interaktiv session.

### Steg 2: JSON och ett skript (20 min)
1. Kör `claude -p "list the test projects in this repo" --output-format json > resultat.json`.
2. Öppna `resultat.json` och hitta fälten `result`, `session_id` och `total_cost_usd`.
3. Skriv ett PowerShell-skript som läser `result` och skriver ut det:
   ```powershell
   $json = Get-Content resultat.json -Raw | ConvertFrom-Json
   $json.result
   ```

**Förväntat:** skriptet skriver ut svaret. Anteckna vilket fält som saknades om något inte stämmer med dokumentationen.

### Steg 3: det som saknas utan person (20 min)
1. Be Claude i headless-läge att ändra en fil: `claude -p "add a one-line comment to README.md"`.
2. Kontrollera utdatan. Vad hände med ändringen? Nekades den?
3. Kör igen med `--allowedTools "Edit"`, och jämför.
4. Kontrollera med `git status` vad som ändrades.

**Förväntat:** utan tillåtelse nekas ändringen, och du ser det i utdatan. Med `--allowedTools "Edit"` går den igenom. Det här är lektionens kärna: godkännandet måste finnas i kommandot.

### Steg 4: smala tillåtelser (20 min)
1. Kör `claude -p "run the tests and report the result" --allowedTools "Bash(dotnet test *)"`.
2. Kör sedan `claude -p "delete the bin folder" --allowedTools "Bash(dotnet test *)"`.
3. Jämför. Vad godkändes, och vad nekades?

**Förväntat:** `dotnet test` körs. Att ta bort `bin` nekas, eftersom kommandot inte matchar den tillåtna regeln.

### Steg 5: `--bare` och en okänd hook (20 min)
1. Skapa en hook i kopians `.claude/settings.json` som skriver en rad till en fil, till exempel `echo hook-ran >> $env:TEMP\hook-check.txt`, med matchern `*`.
2. Kör `claude -p "say hello"`. Öppna `hook-check.txt`.
3. Ta bort filen, och kör `claude --bare -p "say hello"`. Kontrollera igen.

**Förväntat:** utan `--bare` körs hooken, och filen skapas. Med `--bare` körs den inte. Det är därför `--bare` rekommenderas i CI.

### Steg 6: kostnad och gräns (15 min)
1. Kör `claude -p "summarize the README" --output-format json --max-budget-usd 0.05`.
2. Anteckna `total_cost_usd`.
3. Kör `/usage` i en interaktiv session och jämför med det du ser i JSON.

**Förväntat:** en siffra från JSON, och en siffra i `/usage`. De kan skilja sig. Anteckna skillnaden.

### Steg 7: skriv ett skript (15 min)
1. Skriv `review.ps1` som kör `git diff main` och pipar in det till Claude med en tydlig prompt, till exempel `you are a typo linter. report filename:line and the issue. return nothing else.`
2. Kör skriptet på en kort ändring.

**Förväntat:** en lista med eventuella stavfel, och skriptet avslutas med en exit code som du kontrollerat.

---

## Quiz och reflektion (25 min)

1. Vad händer med en åtgärd som kräver godkännande i `claude -p`, om inget annat godkänner den?
2. Vilket utdataformat ger dig `total_cost_usd`, och varför är det en uppskattning?
3. Vad är skillnaden mellan `--allowedTools "Bash"` och `--allowedTools "Bash(git diff *)"`?
4. Varför rekommenderas `--bare` för skript och CI?
5. Vad måste en schemalagd prompt innehålla, som en interaktiv prompt inte behöver?

**Reflektion (skriv 3 meningar):** Vilket av stegen i labbet gav ett resultat du inte väntade dig? Vilken åtgärd skulle du aldrig låta ett skript göra utan person i loopen, och varför? Hur skulle du kontrollera att ett skript misslyckas tydligt när Claude nekas?

Facit finns i [quiz-facit.md](quiz-facit.md), som bara instruktören ska använda.

---

## Referenser

- [Run Claude Code programmatically](https://code.claude.com/docs/en/headless): `-p`, utdataformat, `--allowedTools`, `--bare`, `--permission-prompts`.
- [CLI reference](https://code.claude.com/docs/en/cli-reference): alla flaggor och kommandon.
- [Permission modes](https://code.claude.com/docs/en/permission-modes): lägen i `-p`-körningar.
- [Common workflows](https://code.claude.com/docs/en/common-workflows): schemalagda körningar.
- [Commands](https://code.claude.com/docs/en/commands): `/loop`, `/schedule`, `/usage`.

---

## Slide-outline (ungefär 25 slides för genomgången)

1. Titel: "Lektion 5: Headless och automation".
2. Mål (fem punkter).
3. `claude -p`: samma verktyg, utan person i loopen.
4. Exit code för lyckad och misslyckad körning.
5. Vad som saknas när ingen godkänner.
6. Nekat som standard när ingen svarar.
7. Tre utdataformat, och vad `json` innehåller.
8. Bild: ett skript som läser `result`.
9. `--allowedTools`: bart mot smalt.
10. Regelsyntax: `Bash(git diff *)` och mellanslaget.
11. `--permission-mode`: auto, dontAsk, acceptEdits.
12. `--permission-prompts none`.
13. `denyInsteadOfPrompting` för alla körningar.
14. `--bare`: vad som hoppas över, och varför det rekommenderas.
15. Risken i `-p` utan `--bare`.
16. Fortsätt en konversation med `--resume`.
17. Stdin och 10 MB-gränsen.
18. `--max-budget-usd` och klientuppskattningen.
19. Fyra alternativ för schemalagda körningar.
20. Schemalagda uppgifter kan inte ställa frågor.
21. Checklistan för en säker körning.
22. Vad som är verifierat och vad som ska kontrolleras i labbet (`--max-turns`).
23. Sammanfattning.
24. Labbet i korthet.
25. Innan du går vidare: de fem quizfrågorna och bedömningen.

---

## Bedömning

Lektionen är godkänd när eleven:

- [ ] kan förklara vad som händer med en åtgärd som kräver godkännande i `claude -p`,
- [ ] har kört ett skript som läser `result` från JSON-utdata,
- [ ] har visat skillnaden mellan en körning med och utan `--allowedTools`,
- [ ] har använt en smal regel, till exempel `Bash(dotnet test *)`, och verifierat att en bredare åtgärd nekades,
- [ ] har visat med en testhook att `--bare` hoppar över projektets hooks,
- [ ] kan förklara varför `total_cost_usd` är en uppskattning,
- [ ] klarar minst 4 av 5 quizfrågor.

## Vanliga fel

- **Körningen hänger:** den väntar på ett godkännande. Lägg till `--permission-prompts none` eller ett `--allowedTools`.
- **`result` saknas i JSON:** körningen misslyckades. Kontrollera exit code och hela utdatan.
- **Hook körs i CI:** du kör utan `--bare`. Lägg till flaggan.
- **`--allowedTools` verkar inte gälla:** regelsyntaxen stämmer inte. Kontrollera mellanslaget före `*`.
- **Schemalagd uppgift gör ingenting:** den kan inte ställa frågor. Skriv om prompten så att den säger vad framgång är.
