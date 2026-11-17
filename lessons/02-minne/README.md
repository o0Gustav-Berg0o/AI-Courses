# Lektion 2: CLAUDE.md och minne

**Längd:** 4 timmar.
- Genomgång (läs och diskutera): 2 timmar.
- Labb: 1 timme 35 minuter.
- Quiz och reflektion: 25 minuter.

**Förkunskap**
- Lektion 0 och 1 är genomförda.
- Ett .NET-repo där du kan skapa och ändra filer i en kopia eller på en separat gren.

## Mål

Efter lektionen kan du:

1. Förklara skillnaden mellan CLAUDE.md och auto memory, och vad var och en innehåller.
2. Välja rätt plats för en instruktion: användarnivå, projektnivå, lokal eller regel med `paths:`.
3. Skriva en CLAUDE.md som Claude kan följa, med konkreta och kontrollerbara instruktioner.
4. Använda `/init`, `/memory` och `/doctor` för att skapa och granska minnesfilerna.
5. Förklara varför en instruktion i CLAUDE.md inte är en garanti, och vad du gör när den måste gälla alltid.

---

## Teori (genomgång, 2 timmar)

Varje avsnitt har en källa. Där något är en rekommendation och inte en dokumenterad funktion står det uttryckligen.

### 1. Varje session börjar med tomt minne

Varje Claude Code-session börjar med ett nytt kontextfönster. Det finns två mekanismer som bär kunskap från en session till nästa:

- **CLAUDE.md-filer**: instruktioner som du skriver. Claude läser dem i början av varje session.
- **Auto memory**: anteckningar som Claude skriver själv utifrån dina rättelser och preferenser.

Dokumentationen beskriver skillnaden i en tabell. CLAUDE.md skrivs av dig och innehåller instruktioner och regler. Auto memory skrivs av Claude och innehåller lärdomar och mönster. Båda laddas i varje session.

*Källa: [Memory](https://code.claude.com/docs/en/memory), avsnittet "CLAUDE.md vs auto memory".*

### 2. Var CLAUDE.md-filer kan ligga

Dokumentationen listar platserna i laddningsordning, från bredast till mest specifik:

| Nivå | Plats | Gäller för |
| --- | --- | --- |
| Managed policy | `/etc/claude-code/CLAUDE.md` på Linux och WSL, `/Library/Application Support/ClaudeCode/CLAUDE.md` på macOS, `C:\Program Files\ClaudeCode\CLAUDE.md` på Windows | Alla användare på datorn, organisationens regler |
| Användare | `~/.claude/CLAUDE.md` | Dig, i alla projekt |
| Projekt | `./CLAUDE.md` eller `./.claude/CLAUDE.md` | Alla som arbetar i projektet, via git |
| Lokal | `./CLAUDE.local.md` | Dig, i det här projektet. Läggs i `.gitignore`. |

Claude Code laddar `CLAUDE.md` och `CLAUDE.local.md` från arbetskatalogen och alla kataloger över den. Filerna i underkataloger laddas först när Claude läser, skriver eller ändrar en fil i den katalogen. Allt sammanfogas i kontexten, och det innehåll som ligger längre upp i trädet kommer först.

*Källa: [Memory](https://code.claude.com/docs/en/memory), avsnitten "Choose where to put CLAUDE.md files" och "How CLAUDE.md files load".*

### 3. Skriv instruktioner som kan kontrolleras

Dokumentationen är konkret om hur instruktioner ska skrivas. Claude läser CLAUDE.md som kontext, inte som tvingande konfiguration, så formuleringen påverkar hur väl den följs. Dokumentationen rekommenderar att varje instruktion ska vara tillräckligt konkret för att kunna verifieras.

Två siffror är värda att komma ihåg:

- **Längd:** håll varje CLAUDE.md under ungefär 200 rader. Längre filer tar mer kontext och minskar hur väl instruktionerna följs. Det som bara gäller en del av kodbasen ska i stället ligga i en regel med `paths:`.
- **Konsistens:** om två instruktioner motsäger varandra kan Claude välja en godtyckligt. Granska filerna regelbundet.

*Källa: [Memory](https://code.claude.com/docs/en/memory), avsnittet "Write effective instructions", punkterna om storlek och konsistens.*

### 4. Vad som hör hemma i CLAUDE.md

Dokumentationen säger att du ska tänka på CLAUDE.md som stället där du skriver ner det du annars skulle förklara om och om igen. Typiska innehåll är:

- bygg- och testkommandon, till exempel `dotnet build` och `dotnet test`,
- kodstandarder och namngivning,
- arkitekturbeslut som inte syns i koden,
- vanliga arbetsflöden.

Samtidigt står det i best practices att Claude läser repot själv. Skriv bara ner det som inte går att läsa ut ur koden. En lång beskrivning av mappstrukturen i CLAUDE.md är oftast slöseri.

*Källa: [Memory](https://code.claude.com/docs/en/memory), avsnittet "When to add to CLAUDE.md". [Best practices](https://code.claude.com/docs/en/best-practices), avsnittet "Describe your architecture and file layout in CLAUDE.md" (i tabellen "What you can stop doing": "Claude reads the repo itself. Only write down what isn't in there").*

### 5. `/init` och `/memory`

`/init` skapar en startfil. Claude analyserar kodbasen och skriver en CLAUDE.md med bygg- och testkommandon och de konventioner den hittar. Om en CLAUDE.md redan finns föreslår `/init` förbättringar i stället för att skriva över.

`/memory` öppnar CLAUDE.md-filerna för redigering. Där kan du också aktivera eller stänga av auto memory och se vad som sparats.

*Källa: [Memory](https://code.claude.com/docs/en/memory), avsnittet "Set up a project CLAUDE.md". [Commands](https://code.claude.com/docs/en/commands), tabellerna för `/init` och `/memory`.*

### 6. Auto memory

Auto memory är anteckningar som Claude skriver själv. Varje projekt får en egen minnesmapp under `~/.claude/projects/<projekt>/memory/`. Projektnamnet utgår från git-repot, så alla worktrees och underkataloger delar samma minne.

Minnesmappen har en `MEMORY.md` som index och en fil per minne. De första 200 raderna eller de första 25 KB av `MEMORY.md` laddas i början av varje konversation. Resten laddas inte. Claude förkortar därför filen genom att flytta detaljer till egna filer.

Du kan stänga av auto memory. Med `/memory` ändrar du inställningen i användarinställningarna, och med `autoMemoryEnabled: false` i projektinställningarna stänger du av den för ett enskilt projekt.

*Källa: [Memory](https://code.claude.com/docs/en/memory), avsnitten "Enable or disable auto memory", "Storage location" och "How it works".*

### 7. Regler i `.claude/rules/`

För större projekt kan du dela upp instruktionerna i flera filer under `.claude/rules/`. Varje fil bör handla om ett ämne, till exempel `testing.md` eller `api-design.md`. Regler utan `paths:` laddas vid start, med samma prioritet som `.claude/CLAUDE.md`.

En regel med `paths:` laddas bara när Claude arbetar med en fil som matchar mönstret. Det är ett sätt att spara kontext för instruktioner som bara gäller en del av kodbasen.

*Källa: [Memory](https://code.claude.com/docs/en/memory), avsnittet "Organize rules with `.claude/rules/`". Best practices, avsnittet "Keep cost and context down" (regler och skills).*

### 8. Importera andra filer

En CLAUDE.md kan importera andra filer med `@`-syntax, till exempel `@docs/git-instructions.md`. Importerade filer expanderas och laddas tillsammans med filen som refererar till dem. Skriv `` `@README` `` i backticks om du vill nämna en fil utan att importera den.

*Källa: [Memory](https://code.claude.com/docs/en/memory), avsnittet "Import additional files".*

### 9. CLAUDE.md är kontext, inte en garanti

Dokumentationen är tydlig här. Både CLAUDE.md och auto memory är kontext som Claude läser och tolkar. De är inte tvingande konfiguration. Om en regel måste gälla alltid, till exempel "läs aldrig `.env`", ska du använda en hook eller en deny-regel. Det är ämnet i lektion 4.

Det här är en av de viktigaste sakerna i kursen. En instruktion i CLAUDE.md kan bli följd nio gånger av tio och brytas den tionde. Om det inte är acceptabelt behöver du en mekanism som inte beror på modellens beslut.

*Källa: [Memory](https://code.claude.com/docs/en/memory), avsnittet "CLAUDE.md vs auto memory": "Claude treats them as context, not enforced configuration. To block an action regardless of what Claude decides, use a PreToolUse hook instead."*

### 10. Granska minnet: `/doctor` och `claudeMdExcludes`

`/doctor` är en inbyggd skill som kontrollerar installation, inställningar och CLAUDE.md-problem. Den föreslår åtgärder som Claude tillämpar efter att du bekräftat. Med `/doctor prompt-audit` granskar du dina instruktioner specifikt, inklusive CLAUDE.md, CLAUDE.local.md, AGENTS.md, regler, skills och subagenter under `.claude/` och `~/.claude/`.

I ett stort monorepo kan andra teams CLAUDE.md-filer laddas i onödan. Med inställningen `claudeMdExcludes` kan du hoppa över dem. Organisationens managed CLAUDE.md kan inte exkluderas av utvecklaren.

*Källa: [Commands](https://code.claude.com/docs/en/commands), tabellen för `/doctor`. [Memory](https://code.claude.com/docs/en/memory), avsnitten "Audit your instruction files" och "How CLAUDE.md files load", samt "Exclude specific CLAUDE.md files" för `claudeMdExcludes`.*

### 11. Hur du vet att en instruktion gjorde nytta

Det enklaste sättet är att testa. Be Claude göra något som instruktionen styr, och kontrollera utdatan. Om Claude gör fel, skriv om instruktionen konkret, och testa igen. En instruktion som inte går att testa går inte att kontrollera.

Det här är samma princip som i lektion 0: be om bevis, inte påståenden.

*Källa: [Memory](https://code.claude.com/docs/en/memory), avsnittet "Write effective instructions" (formuleringen att instruktionerna ska vara konkreta nog att verifieras). Principen om bevis kommer från [Best practices](https://code.claude.com/docs/en/best-practices), avsnittet "Give Claude a check to run".*

### Sammanfattning av genomgången

- Det finns två minnen: CLAUDE.md skriver du, auto memory skriver Claude.
- Välj nivå: användare, projekt, lokal eller regel med `paths:`.
- Håll varje fil under ungefär 200 rader. Skriv bara det som inte går att läsa ut ur koden.
- Skriv konkreta instruktioner som går att testa.
- CLAUDE.md är kontext, inte en garanti. Det som alltid måste gälla kräver en hook eller deny-regel.
- `/init` startar en fil, `/memory` redigerar, `/doctor prompt-audit` granskar.

---

## Grundkommandon i den här lektionen

| Kommando | Vad det gör |
| --- | --- |
| `/init` | Skapar en startfil med CLAUDE.md. Föreslår förbättringar om en redan finns. |
| `/memory` | Redigerar CLAUDE.md-filer och styr auto memory. |
| `/doctor` | Kontrollerar installation, inställningar och CLAUDE.md. Föreslår åtgärder. |
| `/doctor prompt-audit [sökväg]` | Granskar instruktioner, valfritt en viss fil eller mapp. |
| `/context` | Visar vilka CLAUDE.md-filer och minnesfiler som laddades. |
| `@sökväg` i en CLAUDE.md | Importerar en annan fil. |

---

## Labb (1 timme 35 minuter)

Labbet görs i ett .NET-repo. Steg 3 och 4 skriver filer, så gör dem i en kopia eller på en separat gren.

### Steg 1: se vad som laddas (10 min)
1. Starta `claude` i repot.
2. Kör `/context` och hitta de CLAUDE.md- och minnesposter som finns.
3. Anteckna om det finns en projekt-CLAUDE.md, en användar-CLAUDE.md och en auto memory-post.

**Förväntat:** en lista över vilka instruktionsfiler som faktiskt laddades. Om det inte finns någon projekt-CLAUDE.md är det resultatet som ska noteras.

### Steg 2: skapa en CLAUDE.md med `/init` (15 min)
1. Kör `/init`.
2. Läs igenom förslaget. Anteckna en rad som är felaktig eller saknas.
3. Låt Claude föreslå rättelser, eller rätta själv.

**Förväntat:** en CLAUDE.md med bygg- och testkommandon. Kontrollera att `dotnet build` och `dotnet test` står med, och att de stämmer med repot. Om en rad är fel är det en observation för reflektionen.

### Steg 3: skriv en konkret instruktion och testa den (20 min)
1. Lägg till en instruktion i CLAUDE.md som går att kontrollera, till exempel: `Use dotnet test --no-build when running tests after a change, unless the build itself failed.`
2. Be Claude köra tester efter en liten ändring, och se om instruktionen följs.
3. Skriv om instruktionen till en mer konkret version om den inte följdes, och testa igen.

**Förväntat:** en instruktion som du kan verifiera med utdatan. Anteckna vad som ändrades mellan första och andra versionen.

### Steg 4: lokal instruktion (15 min)
1. Skapa `CLAUDE.local.md` i repots rot med en personlig preferens, till exempel `Answer in Swedish.`
2. Kontrollera att filen står i `.gitignore`. Lägg till den om den inte finns där.
3. Kör `git status`. Filen ska inte synas.
4. Kör `/context` och kontrollera att den laddades.

**Förväntat:** `CLAUDE.local.md` laddas men syns inte i `git status`.

### Steg 5: regel med `paths:` (15 min)
1. Skapa `.claude/rules/tests.md` med frontmatter:
   ```text
   ---
   paths:
     - "**/*Tests.cs"
   ---
   Use xUnit conventions. Name test methods MethodName_Scenario_ExpectedResult.
   ```
   Anpassa namnkonventionen så att den passar repot, eller använd den som den är.
2. Öppna en vanlig källfil och be Claude läsa den. Kontrollera med `/context` att regeln inte laddades.
3. Öppna en testfil och be Claude läsa den. Kontrollera igen.

**Förväntat:** regeln laddas först när en testfil läses, och inte när en vanlig källfil läses.

### Steg 6: kontrollera med `/doctor` (20 min)
1. Kör `/doctor prompt-audit`.
2. Läs igenom förslagen. Välj ett förslag och be Claude tillämpa det, men bara om du förstår det.
3. Kör `git diff` och granska ändringen.

**Förväntat:** en rapport med förslag. Anteckna minst ett förslag du godkände och ett du avvisade, och varför.

### Steg 7: auto memory (10 min)
1. Ge Claude en korrigering som är värd att spara, till exempel: `In this repo we always use Result types instead of throwing exceptions in services.`
2. Öppna `/memory` och kontrollera om auto memory sparade något.
3. Anteckna om det sparade är korrekt och användbart.

**Förväntat:** en eventuell minnespost. Det är ingen garanti att den sparas, och det är en observation.

---

## Quiz och reflektion (25 min)

1. Vad är skillnaden mellan CLAUDE.md och auto memory?
2. Var skulle du lägga en instruktion som bara gäller dig, och inte teamet?
3. Varför ska en CLAUDE.md hållas under ungefär 200 rader?
4. Varför är CLAUDE.md inte rätt plats för regeln "läs aldrig `.env`"?
5. Vad gör en regel med `paths:` som en vanlig regel utan `paths:` inte gör?

**Reflektion (skriv 3 meningar):** Vilken instruktion i din CLAUDE.md följdes inte, eller följdes bara delvis? Hur skrev du om den, och vad visade testet? Vad skulle du inte skriva ner, eftersom Claude kan läsa det ur koden?

Facit finns i [quiz-facit.md](quiz-facit.md), som bara instruktören ska använda.

---

## Referenser

- [Memory](https://code.claude.com/docs/en/memory): CLAUDE.md-platser, laddning, regler, auto memory, AGENTS.md.
- [Best practices](https://code.claude.com/docs/en/best-practices): vad som ska stå i CLAUDE.md och vad som inte ska stå där.
- [Skills](https://code.claude.com/docs/en/skills): `/doctor`, och hur skills skiljer sig från CLAUDE.md.
- [Commands](https://code.claude.com/docs/en/commands): `/init`, `/memory`, `/doctor`.

---

## Slide-outline (ungefär 25 slides för genomgången)

1. Titel: "Lektion 2: CLAUDE.md och minne".
2. Mål (fem punkter).
3. Två minnen: CLAUDE.md skriver du, auto memory skriver Claude.
4. Tabell: vad var och en innehåller.
5. Platserna i laddningsordning.
6. Bild: hur filerna sammanfogas från rot till arbetskatalog.
7. Underkataloger laddas när Claude rör dem.
8. Konkreta instruktioner, med ett bra och ett dåligt exempel.
9. Under 200 rader: varför det spelar roll.
10. Vad som hör hemma i CLAUDE.md.
11. Vad som inte hör hemma där: det som går att läsa ur koden.
12. `/init` och vad det skriver.
13. `/memory`: redigera och styra auto memory.
14. Auto memory: var den ligger och vad som laddas.
15. Gränsen på 200 rader och 25 KB i MEMORY.md.
16. Stänga av auto memory, per användare eller per projekt.
17. `.claude/rules/` och filer per ämne.
18. Regler med `paths:`: laddas när det behövs.
19. `@`-import i CLAUDE.md.
20. `CLAUDE.local.md` och `.gitignore`.
21. CLAUDE.md är kontext, inte en garanti.
22. Det som alltid måste gälla: hook eller deny-regel (förhandsvisning av lektion 4).
23. `/doctor` och `/doctor prompt-audit`.
24. `claudeMdExcludes` i monorepo.
25. Innan du går vidare: de fem quizfrågorna och bedömningen.

---

## Bedömning

Lektionen är godkänd när eleven:

- [ ] kan förklara skillnaden mellan CLAUDE.md och auto memory,
- [ ] kan välja rätt nivå för en instruktion och motivera valet,
- [ ] har kört `/init` och granskat förslaget rad för rad,
- [ ] har skrivit en instruktion och verifierat att den följdes, eller skrivit om den och testat igen,
- [ ] har en `CLAUDE.local.md` som inte syns i `git status`,
- [ ] kan förklara varför en regel med `paths:` laddas först när en matchande fil läses,
- [ ] kan förklara varför CLAUDE.md inte är en garanti, och vad som är det,
- [ ] klarar minst 4 av 5 quizfrågor.

## Vanliga fel

- **Instruktionen följs inte:** den är för vag, eller så står den i en lång fil där den drunknar. Gör den konkret, och kontrollera att den inte motsägs av en annan rad.
- **`CLAUDE.local.md` syns i `git status`:** den står inte i `.gitignore`. Lägg till den och kontrollera igen.
- **Regeln med `paths:` laddas aldrig:** mönstret matchar inte filerna. Kontrollera mot filernas faktiska sökväg.
- **`/memory` visar ingen auto memory:** auto memory är avstängt, eller så har Claude inte sparat något än.
- **CLAUDE.md växer hela tiden:** flytta detaljer till regler med `paths:` eller till egna filer, och granska med `/doctor prompt-audit`.
