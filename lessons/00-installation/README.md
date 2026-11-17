# Lektion 0: Installation och första sessionen

**Längd:** 4 timmar.
- Genomgång (läs och diskutera): 2 timmar.
- Labb: 1 timme 35 minuter.
- Quiz och reflektion: 25 minuter.

**Förkunskap**
- Ett konto som ger tillgång till Claude Code: Pro, Max, Ultra, Team, Enterprise, Claude Console, eller en molnleverantör som stöds.
- Windows med PowerShell.
- Git installerat. Git for Windows rekommenderas (se avsnittet Installation).
- Ett .NET-repo som du kan läsa i men inte behöver ändra permanent.

## Mål

Efter lektionen kan du:

1. Förklara vad Claude Code gör när du ställer en fråga, och vilka delar du själv ansvarar för.
2. Installera Claude Code på Windows och verifiera installationen.
3. Starta en session, logga in och hitta grundkommandona.
4. Ställa frågor om en kodbas och kontrollera svaren mot koden.
5. Förklara vad permission modes är och vad plan-läge gör.
6. Köra Claude i skriptläge med `claude -p`.
7. Förklara varför ett påstående "klart" från Claude inte räcker som bevis.

---

## Teori (genomgång, 2 timmar)

Den här delen är skriven så att du kan läsa den i förväg eller gå igenom den tillsammans med instruktören. Varje avsnitt har en källa. Där en del är en rekommendation och inte en dokumenterad funktion, står det uttryckligen.

### 1. Vad Claude Code är

Claude Code är ett verktyg som du kör i en terminal. Du öppnar en projektmapp, startar `claude`, och skriver vad du vill. Claude läser filer i projektet, söker i kodbasen, svarar på frågor och kan föreslå eller göra ändringar i filer. Det kan också köra kommandon, till exempel tester, om du tillåter det.

Det viktiga är skillnaden mot en vanlig chatt. I en webbchatt klistrar du in kod och får ett svar. I Claude Code läser verktyget själv den kod som behövs. Dokumentationen beskriver det som att Claude läser filer efter behov, så du behöver inte lägga till kontext manuellt för att få svar om projektet.

Det betyder också att Claude kan ta fel i sina antaganden om kodbasen. Ditt jobb är att granska. Det är inte en egenskap som försvinner med bättre modeller. Det är en egenskap hos hur alla verktyg av det här slaget fungerar, och därför är verifiering en del av varje lektion i kursen.

*Källa: [Quickstart](https://code.claude.com/docs/en/quickstart), steg 3 och 4. [Best practices](https://code.claude.com/docs/en/best-practices), avsnittet "Tell Claude what it can't see".*

### 2. Installation

Det finns flera sätt att installera. På Windows är det vanligaste PowerShell-kommandot:

```powershell
irm https://claude.ai/install.ps1 | iex
```

Efter installationen öppnar du ett nytt terminalfönster och kontrollerar:

```bash
claude --version
```

Svaret är ett versionsnummer följt av `(Claude Code)`. Om kommandot inte hittas ligger installationsmappen inte på PATH. Öppna då ett nytt fönster, och om det inte räcker, se felsökningen i Quickstart.

Det finns alternativ. Med Homebrew kör du `brew install --cask claude-code` på Mac och Linux. Med WinGet kör du `winget install Anthropic.ClaudeCode` på Windows. Det är en viktig skillnad: den native installationen uppdateras automatiskt i bakgrunden, medan Homebrew- och WinGet-installationer inte gör det. Då behöver du köra `winget upgrade Anthropic.ClaudeCode` själv med jämna mellanrum.

Två saker är särskilt viktiga på Windows:

- **Git for Windows** rekommenderas. Med det kan Claude Code använda Bash-verktyget. Utan det används PowerShell som skalverktyg. Båda fungerar, men Bash-verktyget är det som dokumentationen och många exempel utgår från.
- **WSL** behöver inte Git for Windows. Om du arbetar i WSL följer du Linux-instruktionerna i stället.

*Källa: Quickstart, steg 1. Installationsmetoder och uppdateringsbeteende står i samma avsnitt.*

### 3. Konto och inloggning

Claude Code kräver ett konto. Enligt Quickstart kan du logga in med:
- ett Claude-abonnemang: Pro, Max, Ultra, Team eller Enterprise (rekommenderat),
- Claude Console, där du betalar med förladdade krediter,
- en molnleverantör: Amazon Bedrock, Google Cloud eller Microsoft Foundry,
- en självhostad gateway, om din organisation kör en.

Om miljövariabeln `ANTHROPIC_API_KEY` är satt och du godkänner nyckeln när Claude Code frågar, hoppar det över inloggningen. Det är bra att veta när du felsöker. En gammal nyckel i din miljö kan ge ett oväntat konto. Inloggningen lagras, så du loggar inte in igen nästa gång. Byt konto med `/login` i en session.

*Källa: Quickstart, steg 2.*

### 4. Din första session

Starta från projektmappen:

```bash
cd C:\Repos\ditt-repo
claude
```

Claude Code uppmanar dig att logga in första gången. När du är inne visas versionen, den aktuella modellen och arbetsmappen ovanför prompten.

Några saker gör det lättare att komma igång:
- Skriv `/` för att se kommandon och skills. Skriv `/` följt av bokstäver för att filtrera listan.
- Tab kompletterar. Pil upp visar tidigare prompter.
- `/help` visar kommandona.
- `/exit` avslutar. Du kan också trycka Ctrl+D två gånger.
- Du kan skicka en första prompt direkt: `claude "fix the build error"`.

Kommandon gäller bara i början av ett meddelande. Om du skriver `/context` efter lång text är det inte ett kommando.

*Källa: Quickstart, "Shell commands", "Session commands" och "Pro tips for beginners". Kommandoreferensen bekräftar `/help`, `/context` och `/exit`.*

### 5. Kontext: vad Claude vet i en session

Varje session börjar med ett kontextfönster. Det är allt Claude just nu kan se: dina instruktioner, filer den läst, egna svar, och verktygsresultat. Kontextfönstret är en gräns, mätt i tokens. Ett token är ungefär en del av ett ord.

Vissa saker laddas innan du har skrivit något. Enligt dokumentationen är det systemprompten, auto memory, miljöinformation, namnen på MCP-verktyg, beskrivningarna av skills, och dina CLAUDE.md-filer. `/context` visar hur mycket som ligger där. Det är det första kommandot att använda när svaren verkar konstiga.

Två fakta är viktiga för nybörjare:
- De flesta aktuella modeller har ett kontextfönster på en miljon tokens, enligt best practices. Det betyder att kontexten sällan tar slut i en vanlig session.
- När fönstret börjar fyllas komprimerar Claude Code konversationen automatiskt. Du kan också köra `/compact` själv, och ge det en inriktning, till exempel `/compact focus on the auth bug`.

Det som gör kontexten värd att tänka på är inte att den tar slut. Det är att gammalt material kan dra svaren åt fel håll, och att du betalar för varje token i varje tur. Därför rekommenderas `/clear` mellan olika uppgifter.

*Källa: [Explore the context window](https://code.claude.com/docs/en/context-window), och [Best practices](https://code.claude.com/docs/en/best-practices), avsnittet "Keep cost and context down".*

### 6. Permission modes

Permission mode bestämmer vilka åtgärder Claude får göra utan att fråga dig. Den finns i flera lägen: `default`, `acceptEdits`, `plan`, `auto`, `dontAsk`, `bypassPermissions` och `manual`. För den här lektionen behöver du tre av dem:

- **Plan-läge** (`plan`): Claude läser filer och föreslår en plan, men ändrar inga filer förrän du godkänt planen. Statusraden visar `⏸ plan mode on` när det är aktivt. Det är det säkraste läget när du vill förstå en kodbas eller granska en ändring innan den görs.
- **Auto mode** (`auto`): Claude arbetar utan att fråga om rutinmässiga godkännanden. En separat klassificerare granskar varje åtgärd innan den körs. Enligt best practices startar interaktiva sessioner i terminal och VS Code i auto mode.
- **Manual**: du godkänner varje ändring och varje kommando som inte är skrivskyddat själv.

Du byter läge i sessionen med `Shift+Tab`, och från kommandoraden med till exempel `claude --permission-mode plan`.

Auto mode är inte samma sak som ingen kontroll. Klassificeraren blockerar särskilt riskfyllda saker: `curl | bash`, produktionsdeployer och migreringar, force-push över andras arbete, känsliga data som skickas till externa adresser, och åtgärder som verkar styrda av innehåll Claude läst i stället för av dig. Dina deny-regler och ask-regler gäller före klassificeraren. En deny-regel blockerar därför även i auto mode.

*Källa: [Common workflows](https://code.claude.com/docs/en/common-workflows), avsnittet "Plan before editing". [Best practices](https://code.claude.com/docs/en/best-practices), avsnitten "Change the permission mode" och "Enforce the rules that can't have exceptions". Lägesnamnen är verifierade i [CLI reference](https://code.claude.com/docs/en/cli-reference).*

### 7. Hur du ställer en bra fråga

Dokumentationen är tydlig här. Claude kan läsa koden, men den kan inte läsa din tanke. Ju mer du säger om vad som är fel och hur ett klart resultat ser ut, desto mindre gissar den.

Jämför två prompter:
- Dålig: *"fix the login bug"*
- Bättre: *"users report login fails after the session times out. look at token refresh in src/auth/, write a failing test that reproduces it, then fix it"*

Den bättre prompten namnger filen, beskriver symptomet och säger vad "klart" betyder. Det sparar en runda med korrigeringar.

Tre tekniker används i kursen:
- **Peka på filer.** Skriv `@` följt av sökvägen så att filinnehållet tas med i meddelandet. Det fungerar för filer och mappar.
- **Skicka in utdata.** Pipa in loggar eller felmeddelanden: `cat error.log | claude -p "what's going on here"`.
- **Intervjua dig först.** Om du inte vet vad du vill, be Claude fråga dig tills det går att skriva en spec, och spara den i `SPEC.md`. Starta sedan en ny session för bygget. Det används i lektion 3.

Ordet "be ambitious" är värt att känna till. Claude tenderar att bygga den minsta versionen som uppfyller kravet. Om du vill ha fler kanter täckta, säg det uttryckligen.

*Källa: [Best practices](https://code.claude.com/docs/en/best-practices), avsnitten "Write a specific prompt" och "Brainstorm with Claude first". Teknikerna med `@` och pipe verifieras i [Common workflows](https://code.claude.com/docs/en/common-workflows), avsnittet "Reference files and directories".*

### 8. Verifiera, inte bara läsa

Det vanligaste problemet med AI-verktyg är inte att de svarar fel. Det är att de säger att de är klara utan att ha kontrollerat det. Enligt best practices beror det oftast på att Claude inte hade något sätt att kontrollera sitt eget arbete.

Därför är den viktigaste vanan i kursen att fråga efter bevis: kommandot Claude körde och dess utdata, eller en skärmbild. "All tests pass" säger nästan ingenting. Utdatan visar vad som faktiskt hände.

För nybörjare: när du ber om något, säg vilket kommando som bevisar att det fungerar. I ett .NET-repo kan det vara `dotnet build` eller `dotnet test`. Det är samma princip som i en vanlig testprocess, men Claude kan köra det själv, och då kan den rätta sina egna fel innan du ser dem.

I lektion 0 gör du en liten ändring i en kopia och granskar diffen innan du godkänner den. Det är samma princip i miniatyr.

*Källa: [Best practices](https://code.claude.com/docs/en/best-practices), avsnittet "Give Claude a check to run": "Whichever option you use, ask Claude for evidence".*

### 9. Skriptläge

`claude -p "fråga"` kör en fråga och avslutar. Det är samma verktyg, men utan interaktiv session, och det kan köras från ett skript eller i CI. Du kan pipa in innehåll och få ut ett svar. Med `--output-format json` får du ett svar du kan läsa programmatiskt.

Det är viktigt att förstå vad skriptläget inte har: ingen person som godkänner varje steg. Därför är det en egen lektion senare i kursen, där vi behandlar `--allowedTools`, `--max-turns` och `--max-budget-usd`. I lektion 0 använder du det bara för att läsa svar.

*Källa: [CLI reference](https://code.claude.com/docs/en/cli-reference), avsnittet "CLI commands" och flaggorna `--output-format`, `--max-turns` och `--max-budget-usd`. Pipe-exemplet står i [Best practices](https://code.claude.com/docs/en/best-practices).*

### 10. Vanliga missförstånd

Dokumentationens avsnitt "What you can stop doing" tar upp flera råd som gällde för äldre modeller men inte längre behövs. Det är bra att känna till dem, för de dyker upp i gamla bloggar och kursmaterial:

- **"Kontexten tar alltid slut, så hantera den hårt."** Det är sällan ett problem i dag. `/clear` mellan olika uppgifter är ändå bra, av kostnads- och fokusskäl.
- **"Skriv IMPORTANT eller VERSALER för att Claude ska lyssna."** Det hjälper sällan. Om en regel måste följas alltid ska den vara en hook eller en deny-regel, inte en betonad rad i CLAUDE.md.
- **"Använd underagenter för att skydda kontexten."** Underagenter är bra för parallellt arbete och för att hålla stora utdata borta, men de kostar extra tokens.
- **"Skriv `think harder` i prompten."** Det är effortnivån som styr hur länge Claude tänker, inte ordvalet. Standardnivån är enligt dokumentationen `medium`. Du ändrar den med `/effort`.

*Källa: [Best practices](https://code.claude.com/docs/en/best-practices), tabellen "What you can stop doing", och avsnittet "Change the model and effort".*

### 11. Vem ansvarar för vad

Det här är det viktigaste för en nyexaminerad utvecklare att förstå. Det finns två sorters instruktioner i Claude Code:

- **Icke-deterministiska:** det du skriver i prompten, i CLAUDE.md och i skills. Claude läser dem och bestämmer själv vad den gör med dem. Den följer dem oftast, men inte alltid.
- **Deterministiska:** hooks, deny-regler och managed settings. Claude Code tillämpar dem varje gång, oavsett vad modellen bestämt.

Dokumentationen säger uttryckligen att CLAUDE.md-innehåll är kontext, inte tvingande konfiguration. Det gör att du ska använda CLAUDE.md för riktlinjer och hooks eller deny-regler för regler som aldrig får brytas. Det är ämnet för lektion 2 och 4.

Det betyder också att du äger resultatet. Claude kan föreslå ändringen, men du granskar den, du godkänner den, och du ansvarar för vad som går till produktion.

*Källa: [Memory](https://code.claude.com/docs/en/memory), avsnittet "CLAUDE.md vs auto memory". [Best practices](https://code.claude.com/docs/en/best-practices), avsnittet "Deterministic and non-deterministic instructions".*

### 12. Kontroll under körningen

Du behöver inte vänta tills Claude är klar för att påverka den. Tre kontroller är värda att kunna utantill:
- `Esc` stoppar Claude mitt i en åtgärd och behåller det som redan gjorts. Du kan styra om utan att förlora arbete.
- `/rewind` öppnar en meny där du kan gå tillbaka till ett tidigare läge i koden, konversationen eller båda. Det gör det tryggare att prova en djärvare lösning.
- `/clear` startar om. Om du har korrigerat samma sak två gånger och den fortfarande är fel, är en ny session med en bättre prompt oftast bättre än en tredje korrigering.

Var uppmärksam på en begränsning: `/rewind` återställer de filer Claude ändrat med sina egna filverktyg. En fil som bara ändrats av ett Bash-kommando återställs inte. Git är fortfarande ditt säkerhetsnät.

*Källa: [Best practices](https://code.claude.com/docs/en/best-practices), avsnittet "Steer the session". Begränsningen om Bash-ändringar står i samma avsnitt.*

### 13. Kostnad och användning

Varje tur skickar hela konversationen på nytt. Ett långt, orelaterat samtal blir därför dyrare för varje meddelande. `/usage` visar var användningen går, till exempel per underagent och per MCP-server. Det är det andra kommandot att kunna när något kostar mer än väntat.

*Källa: [Best practices](https://code.claude.com/docs/en/best-practices), avsnittet "Keep cost and context down".*

### Sammanfattning av genomgången

- Claude Code läser ditt projekt själv och kan föreslå och göra ändringar. Du granskar och godkänner.
- Installera med PowerShell, verifiera med `claude --version`, och använd Git for Windows.
- Starta i projektmappen. Kommandon börjar med `/` och gäller bara i början av ett meddelande.
- Kontexten laddas med dina instruktioner och det du läser. `/context` visar vad som ligger där.
- Permission mode styr vad Claude får göra utan att fråga. Plan-läge ändrar inga filer.
- Ställ specifika frågor. Peka på filer med `@`.
- Kräv bevis, inte påståenden.
- Använd `claude -p` när det inte finns någon person i loopen, och räkna med att det då inte finns någon som godkänner.
- Hooks och deny-regler är det som alltid gäller. CLAUDE.md är riktlinjer.

---

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
| `Esc` | Stoppar Claude mitt i en åtgärd. |

## Labb (1 timme 35 minuter)

### Steg 1: installera (10 min)
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

### Steg 2: första sessionen (10 min)
Gå till ditt .NET-repo och starta:
```bash
cd C:\Repos\ditt-repo
claude
```
Logga in när du uppmanas. Skriv `/help` och läs igenom listan.
**Förväntat:** en lista över kommandon och en prompt där du kan skriva.

### Steg 3: ställ tre frågor (15 min)
Ställ dessa frågor, en i taget:
- `what does this project do?`
- `where is the main entry point?`
- `explain the folder structure`

Kontrollera varje svar mot koden själv. Anteckna en fråga där svaret var fel, ofullständigt eller gissat.
**Förväntat:** tre svar, där minst två stämmer med koden. Skillnaderna är lektionens observation.

### Steg 4: se vad som laddats (10 min)
Kör:
```text
/context
```
Anteckna tre saker som står i listan och vad du tror att de gör. Du använder listan i lektion 1.
**Förväntat:** en lista över kontextens delar.

### Steg 5: plan-läge (15 min)
1. Tryck `Shift+Tab` tills statusraden visar plan-läge.
2. Ställ frågan: `what would you change to add logging to the main service?`
3. Kontrollera att inga filer har ändrats. Använd `git status` i ett annat fönster om du är osäker.
4. Tryck `Shift+Tab` för att gå tillbaka till normalläge.

**Förväntat:** en plan i svaret och inga ändrade filer.

### Steg 6: återuppta sessionen (10 min)
1. Skriv `/exit`.
2. Starta om med:
```bash
claude -c
```
**Förväntat:** den föregående konversationen finns kvar i sessionen.

### Steg 7: skriptläge (10 min)
Kör utanför sessionen, i PowerShell:
```bash
claude -p "what does this project do? answer in three sentences"
```
Jämför svaret med svaret i steg 3. Skriv ned två skillnader mellan `claude` och `claude -p`.
**Förväntat:** ett svar som skrivs ut och att programmet avslutas av sig självt.

### Steg 8: en riktig ändring i en säker kopia (15 min)
Gör det i en kopia av repot eller på en separat gren, så att din huvudkod inte påverkas.
1. Kontrollera att arbetskatalogen är ren: `git status`.
2. Starta Claude och be om en liten ändring: `add a one-line comment to the main file`.
3. Granska diffen innan du godkänner. Stoppa med `Esc` om något ser fel ut.
4. Kasta ändringen när du är klar: `git checkout -- .` (gäller bara kopian, aldrig ditt huvudrepo).

**Förväntat:** en godkänd ändring som du kan se i `git diff`, och en ren arbetskatalog efter steg 4.

## Quiz och reflektion (25 min)

1. Vilket kommando visar installerad version av Claude Code?
2. Vad gör `claude -c`?
3. Vad är skillnaden mellan `claude "fråga"` och `claude -p "fråga"`?
4. Varför rekommenderas Git for Windows?
5. Vad händer när du trycker `Shift+Tab` i en session?

**Reflektion (skriv 3 meningar):** Vilket av svaren i labbet överraskade dig, och hur kontrollerade du det?

Facit finns i [quiz-facit.md](quiz-facit.md), som bara instruktören ska använda.

## Referenser

- [Quickstart](https://code.claude.com/docs/en/quickstart): installation, inloggning, första sessionen, grundkommandon.
- [Best practices](https://code.claude.com/docs/en/best-practices): specifika prompter, verifiering, permissions, kostnad.
- [Explore the context window](https://code.claude.com/docs/en/context-window): vad som laddas och när.
- [CLI reference](https://code.claude.com/docs/en/cli-reference): alla flaggor och kommandon.
- [Commands](https://code.claude.com/docs/en/commands): slash-kommandon.
- [Common workflows](https://code.claude.com/docs/en/common-workflows): plan-läge, återupptagning och pipe.

## Slide-outline (ungefär 25 slides för genomgången)

1. Titel: "Lektion 0: Installation och första sessionen".
2. Mål (sju punkter).
3. Vad Claude Code är: läser, föreslår, du granskar.
4. Skillnaden mot en webbchatt.
5. Installation på Windows, med kommandot i kodblock.
6. Native, Homebrew och WinGet: vad som uppdateras automatiskt.
7. Git for Windows och Bash-verktyget.
8. Inloggning: de fyra kontotyperna.
9. `ANTHROPIC_API_KEY` och varför det kan ge oväntat konto.
10. Första sessionen: start, `/`, Tab, pil upp, `/exit`.
11. Vad som laddas i kontextfönstret (bild).
12. Komprimering och `/compact` med inriktning.
13. Varför `/clear` mellan uppgifter ändå är bra.
14. Permission modes (tabell med sju lägen).
15. Plan-läge: vad det gör och vad det inte gör.
16. Auto mode och klassificeraren, med listan över blockerade saker.
17. Deny-regler gäller före klassificeraren.
18. Dålig och bättre prompt sida vid sida.
19. `@`, pipe och intervju med `SPEC.md`.
20. "Be ambitious" och när det är rätt.
21. Verifiera: be om bevis, inte påståenden.
22. Skriptläge med `claude -p` och `--output-format json`.
23. Fyra vanliga missförstånd från best practices.
24. Icke-deterministiskt mot deterministiskt (bild).
25. Kontroll under körningen: `Esc`, `/rewind`, `/clear`, och vad `/rewind` inte återställer.
26. Labbet i korthet: de åtta stegen med förväntat resultat.
27. Innan du går vidare: de tre frågor eleven ska kunna svara på, och bedömningens checklista.

## Bedömning

Lektionen är godkänd när eleven:

- [ ] kan förklara skillnaden mellan Claude Code och en webbchatt,
- [ ] kör `claude --version` och kan förklara vad svaret betyder,
- [ ] har ställt de tre frågorna och kontrollerat svaren mot koden,
- [ ] kan förklara skillnaden mellan `claude` och `claude -p`,
- [ ] har växlat till plan-läge och verifierat att inga filer ändrades,
- [ ] har återupptagit en session med `claude -c`,
- [ ] kan förklara varför ett påstående om "klart" inte räcker som bevis,
- [ ] klarar minst 4 av 5 quizfrågor.

## Vanliga fel

- **`claude` hittas inte efter installation:** öppna ett nytt terminalfönster. Om det inte hjälper, kontrollera PATH. Se Quickstart.
- **`The token '&&' is not a valid statement separator`:** du kör CMD-kommandon i PowerShell. Använd PowerShell-kommandot i labbet.
- **Ingen konversation hittas med `claude -c`:** du startade i en annan mapp. `claude -c` läser den senaste konversationen i den aktuella mappen.
- **Fel konto används:** `ANTHROPIC_API_KEY` kan vara satt i miljön. Kontrollera och använd `/login` för att byta.
- **Svaret är fel men låter säkert:** be Claude visa filen eller raden den byggde svaret på. Det är ett bättre test än att läsa svaret.
