# Lektion 6: Team, styrning och kostnad

**Längd:** 4 timmar.
- Genomgång (läs och diskutera): 2 timmar.
- Labb: 1 timme 35 minuter.
- Quiz och reflektion: 25 minuter.

**Förkunskap**
- Lektion 0 till 5 är genomförda.
- Tillgång till ett repo som flera personer arbetar i, eller en kopia som simulerar det.

## Mål

Efter lektionen kan du:

1. Förklara skillnaden mellan regler som styr beteende (CLAUDE.md) och regler som tvingas fram (inställningar, hooks, deny-regler).
2. Använda `claudeMdExcludes` för att hoppa över CLAUDE.md-filer som inte hör till dig.
3. Förklara var kostnaden kommer ifrån i en lång session, och vilka åtgärder som minskar den.
4. Läsa `/usage` och förklara vad uppdelningen per skill, underagent, plugin och MCP-server visar.
5. Förklara hur auto mode kan konfigureras för ett team, och vad som alltid är blockerat.

---

## Teori (genomgång, 2 timmar)

Varje avsnitt har en källa. Där något är en rekommendation står det uttryckligen.

### 1. Två sorters styrning

Dokumentationen skiljer på två sätt att styra Claude i en organisation:

- **Beteende** styrs med CLAUDE.md. Det är riktlinjer: kodstil, arkitektur, arbetssätt.
- **Teknisk tillämpning** styrs med managed settings, hooks och deny-regler. Det tvingas fram.

Dokumentationen formulerar det som att managed settings används för teknisk tillämpning, och CLAUDE.md för beteendevägledning. Den som ansvarar för teamet bör använda båda, men inte blanda ihop dem.

*Källa: [Memory](https://code.claude.com/docs/en/memory), avsnittet "Manage CLAUDE.md for large teams", tabellen som jämför managed CLAUDE.md med managed settings. [Managed settings](https://code.claude.com/docs/en/managed-settings) är sidan för den tekniska tillämpningen.*

### 2. Organisationens CLAUDE.md

En organisation kan distribuera en CLAUDE.md som gäller alla användare på en dator, i alla repon:

- macOS: `/Library/Application Support/ClaudeCode/CLAUDE.md`
- Linux och WSL: `/etc/claude-code/CLAUDE.md`
- Windows: `C:\Program Files\ClaudeCode\CLAUDE.md`

Den laddas före användarens och projektets CLAUDE.md. Utvecklare kan exkludera andra CLAUDE.md-filer med `claudeMdExcludes`, men inte den managed CLAUDE.md.

*Källa: [Memory](https://code.claude.com/docs/en/memory), avsnitten "Deploy organization-wide CLAUDE.md" och "Choose where to put CLAUDE.md files".*

### 3. `claudeMdExcludes`

I ett stort monorepo kan andra teams CLAUDE.md-filer laddas i onödan. Med `claudeMdExcludes` hoppar du över dem. Det används för att hålla kontexten relevant för ditt arbete.

Exkluderingen gäller bara filerna du väljer. Den organisationens managed CLAUDE.md kan du inte exkludera.

*Källa: [Memory](https://code.claude.com/docs/en/memory), avsnittet "How CLAUDE.md files load" (stycket om monorepos och `claudeMdExcludes`), och avsnittet "Exclude specific CLAUDE.md files".*

### 4. Tvingande regler: hooks och deny-regler

Det som ska gälla alltid ska vara tekniskt tillämpat. Det är hooks (lektion 4), deny-regler och managed settings. Dokumentationen säger att deny-regler gäller i alla lägen, även `bypassPermissions`.

Hooks kan också stängas av. `disableAllHooks` sätter alla hooks ur kraft. `allowManagedHooksOnly` låter bara hooks från managed settings köras. Det är ett sätt för en organisation att säkerställa att det är de egna hooks som gäller.

*Källa: [Permission modes](https://code.claude.com/docs/en/permission-modes), avsnittet "Deny rules and precedence". [Hooks](https://code.claude.com/docs/en/hooks) och [Keep Claude working toward a goal](https://code.claude.com/docs/en/goal), avsnittet "Requirements", för `disableAllHooks` och `allowManagedHooksOnly`.*

### 5. Auto mode för ett team

Auto mode har en klassificerare som granskar varje åtgärd. Dokumentationen beskriver en fast beslutsordning och en lista över saker som blockeras som standard, till exempel:

- nedladdning och körning av kod, som `curl | bash`,
- produktionsdeployer och migreringar,
- force-push,
- `git reset --hard`, `git checkout -- .`, `git clean -fd`,
- `terraform destroy` och liknande.

Standardregler kan kompletteras. Dokumentationen nämner `autoMode.allow` i inställningarna, och ett kommando, `/auto-mode-setup`, som kan föreslå en konfiguration utifrån projektet och de senaste sessionerna. Du granskar nekanden i `/permissions`.

Dokumentationen säger också: "Auto mode reduces permission prompts but does not guarantee safety." Den är inte ett skyddsnät i sig.

*Källa: [Permission modes](https://code.claude.com/docs/en/permission-modes), avsnitten "Auto mode and the classifier" och "Eliminate prompts with auto mode". [Commands](https://code.claude.com/docs/en/commands), tabellen för `/permissions`. Konfigurationsdetaljerna finns i [Auto mode configuration](https://code.claude.com/docs/en/auto-mode-config), som inte är läst i sin helhet i kursen.*

### 6. Kostnad: var den kommer ifrån

Varje tur skickar hela konversationen igen. Kostnaden växer därför med längden på sessionen, och den lägre kostnaden för cachade delar gäller bara så länge cachen är varm. Den varar ungefär en timme på ett abonnemang och fem minuter på en API-nyckel, molnleverantör eller usage credits.

Bakgrundsarbete kostar också utan att någon skriver. Det gäller `/loop`, ett mål som kontrolleras efter varje tur, och underagenter.

*Källa: [Best practices](https://code.claude.com/docs/en/best-practices), avsnittet "Keep cost and context down". [Prompt caching](https://code.claude.com/docs/en/prompt-caching) för cachetiderna, och [Run Claude Code programmatically](https://code.claude.com/docs/en/headless) för bakgrundsarbetet i `-p`.*

### 7. `/usage` och uppdelningen

`/usage` visar sessionens kostnad, användningsgränser och aktivitet. På Pro, Max, Ultra, Team och Enterprise delas användningen upp per skill, underagent, plugin och MCP-server. Dokumentationen flaggar också mönster som långt kontext eller frekventa cache-missar när de passerar 10 procent av användningen.

Det ger ett team ett konkret underlag: om en MCP-server står för en stor del av kostnaden, är det den som ska granskas.

*Källa: [Best practices](https://code.claude.com/docs/en/best-practices), avsnittet "Keep cost and context down". [Commands](https://code.claude.com/docs/en/commands), tabellen för `/usage`.*

### 8. Kostnad i skript

I `--output-format json` finns `total_cost_usd` och en kostnad per modell. Ett skript kan därför spåra kostnaden utan att läsa fakturan. Värdet är en klientuppskattning, och kan skilja sig från det faktiska beloppet.

*Källa: [Run Claude Code programmatically](https://code.claude.com/docs/en/headless), avsnittet "Pipe data through Claude".*

### 9. Kostnadsåtgärder för ett team

Det här är kursens rekommendation, byggd på dokumentationens delar:

1. **Mät först.** Läs `/usage` för en vecka av arbete, och hitta den största posten.
2. **Håll CLAUDE.md kort.** Under ungefär 200 rader, och uppdelat i regler med `paths:`.
3. **Exkludera det som inte hör till dig**, med `claudeMdExcludes` i monorepo.
4. **Stäng av onödigt bakgrundsarbete**, till exempel ett `/loop` som ingen längre läser.
5. **Använd underagenter** för stora läsningar, men räkna med att de också kostar.
6. **Sätt gränser i skript**, med `--max-budget-usd`.

*Källa: Kursens rekommendation. Punkterna 1 till 6 har sina källor i avsnitten 3 till 8.*

### 10. Vad som är verifierat och vad som inte är det

Den här lektionen bygger på dokumentationen för CLAUDE.md, hooks, behörigheter, `/usage` och `/goal`. Följande är inte läst i sin helhet, och ska kontrolleras innan kursen ges:

- Managed settings: vilka nycklar som finns och hur de distribueras.
- Auto mode-konfigurationen (`autoMode`), inklusive `/auto-mode-setup`.
- Exakta regler för `claudeMdExcludes`-mönster.

*Källa: [sources.md](../../sources.md). Den här listan uppdateras där underlaget verifierats.*

### Sammanfattning av genomgången

- CLAUDE.md styr beteende. Hooks och deny-regler tvingar fram.
- Organisationens CLAUDE.md kan inte exkluderas av utvecklare.
- `claudeMdExcludes` hoppar över andras CLAUDE.md i monorepo.
- Auto mode har en fast beslutsordning. Deny-regler gäller före klassificeraren.
- Kostnaden växer med sessionens längd. `/usage` visar var den går.
- Mät först, och åtgärda sedan det största.

---

## Grundkommandon i den här lektionen

| Kommando | Vad det gör |
| --- | --- |
| `/usage` | Visar kostnad, gränser och uppdelning per skill, underagent, plugin och MCP-server. |
| `/cost` och `/stats` | Aliaser för `/usage`. |
| `/permissions` | Visar regler per nivå, och granskar nekanden i auto mode. |
| `/memory` | Redigerar CLAUDE.md och styr auto memory. |
| `/doctor prompt-audit` | Granskar instruktionerna i projektet och användaren. |
| `/hooks` | Visar hook-konfigurationerna. |

---

## Labb (1 timme 35 minuter)

Labbet kan göras i ett repo som du har. Om du arbetar ensam simulerar du teamet med en separat användar-CLAUDE.md och en projekt-CLAUDE.md.

### Steg 1: mät (15 min)
1. Kör en normal arbetssession på ungefär 15 minuter i ett .NET-repo.
2. Kör `/usage` i slutet, och anteckna sessionens kostnad.
3. Anteckna den största posten i uppdelningen, om den visas.

**Förväntat:** en siffra för sessionen och en bild av var den gick. Om uppdelningen inte visas i din plan, anteckna det som ett resultat.

### Steg 2: två nivåer av CLAUDE.md (20 min)
1. Skapa en användar-CLAUDE.md i `~/.claude/CLAUDE.md` med en instruktion som bara gäller dig, till exempel `Answer in Swedish.`
2. Skapa en projekt-CLAUDE.md med en instruktion som gäller teamet, till exempel `Run dotnet test before reporting a task as done.`
3. Kör `/context` och kontrollera att båda laddades.
4. Kör `/memory` och kontrollera vilka filer som visas.

**Förväntat:** båda filerna syns i `/context`. Anteckna vilken som laddades först.

### Steg 3: exkludera en CLAUDE.md (20 min)
1. Skapa en underkatalog med en egen `CLAUDE.md`, till exempel `src/legacy/CLAUDE.md`, med en instruktion som inte hör till dig.
2. Be Claude läsa en fil i underkatalogen, och kontrollera med `/context` att filen laddades.
3. Lägg till `claudeMdExcludes` i projektets inställningar för att exkludera den. Kontrollera syntaxen mot dokumentationen i `/memory`.
4. Be Claude läsa filen igen, och kontrollera.

**Förväntat:** filen laddas inte efter exkluderingen. Anteckna om syntaxen fungerade direkt, eftersom den inte är läst i sin helhet i kursen.

### Steg 4: tvinga fram en regel (20 min)
1. Kom ihåg lektion 4: skapa en deny-regel för `Read(./.env)`, eller en hook som blockerar en fil.
2. Förklara i några meningar vilken av de två som bör användas för "hemligheter ska aldrig läsas", och varför.
3. Testa regeln med en fejkad `.env`.

**Förväntat:** regeln blockerar, och du kan motivera att den är bättre än en CLAUDE.md-rad.

### Steg 5: auto mode-nekanden (15 min)
1. Byt till auto mode med `Shift+Tab`.
2. Be Claude göra något som klassificeraren sannolikt blockerar, till exempel `run git reset --hard in this repo`. Kör det i kopian.
3. Öppna `/permissions` och granska nekandena.

**Förväntat:** Claude nekas, och nekandet syns i `/permissions`. Anteckna vad som stod i nekandet.

### Steg 6: kostnadsplan (20 min)
1. Skriv en plan i fem punkter för att minska kostnaden i ditt team, utifrån det du mätte i steg 1.
2. Varje punkt ska ha en mätbar uppföljning. Till exempel: "`/usage` före och efter en vecka".

**Förväntat:** en plan i fem punkter, där varje punkt går att följa upp med en siffra.

---

## Quiz och reflektion (25 min)

1. Varför ska en organisations regel som måste gälla alltid vara en hook eller en deny-regel?
2. Kan en utvecklare exkludera organisationens managed CLAUDE.md med `claudeMdExcludes`?
3. Varför växer kostnaden i en lång session även när svaren är korta?
4. Vad visar uppdelningen i `/usage` per MCP-server?
5. Varför är auto mode inte ett skyddsnät i sig?

**Reflektion (skriv 3 meningar):** Vilken post i din kostnad var större än du trodde? Vilken regel i ditt team skulle du flytta från CLAUDE.md till en tvingande mekanism? Vad skulle du inte kunna kontrollera ännu, eftersom det inte är verifierat i kursen?

Facit finns i [quiz-facit.md](quiz-facit.md), som bara instruktören ska använda.

---

## Referenser

- [Memory](https://code.claude.com/docs/en/memory): organisationens CLAUDE.md, `claudeMdExcludes`, monorepo.
- [Managed settings](https://code.claude.com/docs/en/managed-settings): tekniska inställningar för en organisation.
- [Permission modes](https://code.claude.com/docs/en/permission-modes): deny-regler, auto mode.
- [Auto mode configuration](https://code.claude.com/docs/en/auto-mode-config): `autoMode`, granskning av nekanden.
- [Best practices](https://code.claude.com/docs/en/best-practices): kostnad och kontext.
- [Commands](https://code.claude.com/docs/en/commands): `/usage`, `/permissions`, `/memory`.

---

## Slide-outline (ungefär 25 slides för genomgången)

1. Titel: "Lektion 6: Team, styrning och kostnad".
2. Mål (fem punkter).
3. Två sorters styrning: beteende och tvång.
4. Tabell: CLAUDE.md mot managed settings.
5. Organisationens CLAUDE.md: platserna per operativsystem.
6. Vad utvecklare kan och inte kan exkludera.
7. `claudeMdExcludes` i ett monorepo.
8. Tvingande regler: deny-regler i alla lägen.
9. `disableAllHooks` och `allowManagedHooksOnly`.
10. Auto mode: beslutsordningen.
11. Blockerade som standard: `curl | bash`, deployer, force-push.
12. Anpassa med `autoMode.allow` och `/auto-mode-setup`.
13. "Auto mode reduces prompts but does not guarantee safety."
14. Kostnad: varje tur skickar hela konversationen.
15. Cachen: en timme på abonnemang, fem minuter på API.
16. Bakgrundsarbete som kostar utan att någon skriver.
17. `/usage`: uppdelningen per skill, underagent, plugin och MCP.
18. Flagga vid 10 procent av användningen.
19. `total_cost_usd` i skript, som uppskattning.
20. Sex kostnadsåtgärder för ett team.
21. Mät först, åtgärda det största.
22. Vad som är verifierat och vad som ännu inte är det.
23. Sammanfattning.
24. Labbet i korthet.
25. Innan du går vidare: de fem quizfrågorna och bedömningen.

---

## Bedömning

Lektionen är godkänd när eleven:

- [ ] kan förklara skillnaden mellan CLAUDE.md och tvingande regler, och väljer rätt för ett givet exempel,
- [ ] har läst `/usage` och kan beskriva den största posten,
- [ ] kan förklara varför en organisations managed CLAUDE.md inte kan exkluderas,
- [ ] har testat en deny-regel eller hook som gäller oavsett modellens beslut,
- [ ] kan förklara vad auto mode blockerar som standard, och var nekandena granskas,
- [ ] har skrivit en kostnadsplan där varje punkt går att följa upp,
- [ ] klarar minst 4 av 5 quizfrågor.

## Vanliga fel

- **`/usage` visar ingen uppdelning:** uppdelningen gäller bara vissa abonnemang. Anteckna det och använd totalen.
- **`claudeMdExcludes` har ingen effekt:** syntaxen stämmer inte, eller filen är organisationens managed CLAUDE.md, som inte kan exkluderas.
- **Auto mode blockerar allt:** klassificeraren nekar efter upprepade försök. Granska nekandena i `/permissions`, och justera med `autoMode.allow` i stället för att stänga av granskningen.
- **Kostnaden går inte ner:** den största posten är ofta ett långt samtal, inte en enskild fråga. Dela upp med `/clear`.
