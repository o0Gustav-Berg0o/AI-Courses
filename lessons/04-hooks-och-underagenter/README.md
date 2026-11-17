# Lektion 4: Hooks, behörigheter och underagenter

**Längd:** 4 timmar.
- Genomgång (läs och diskutera): 2 timmar.
- Labb: 1 timme 35 minuter.
- Quiz och reflektion: 25 minuter.

**Förkrav**
- Lektion 0 till 3 är genomförda.
- Ett .NET-repo där du kan skapa filer i `.claude/` i en kopia eller på en separat gren.
- Kunskap om JSON och PowerShell på en grundnivå.

## Mål

Efter lektionen kan du:

1. Förklara skillnaden mellan regler som Claude tolkar och regler som Claude Code tillämpar.
2. Skriva en hook i `settings.json` som blockerar en åtgärd, och förklara vad exit code 2 gör.
3. Skriva en deny-regel, och förklara varför den gäller även i auto mode.
4. Skapa en underagent i `.claude/agents/` och förklara vad dess egen kontext ger.
5. Använda `--worktree` för att köra parallella sessioner utan att ändringarna kolliderar.

---

## Teori (genomgång, 2 timmar)

Varje avsnitt har en källa. Där något är en rekommendation står det uttryckligen.

### 1. Två sorters instruktioner

Det finns två sätt att styra Claude:

- **Det Claude läser och tolkar:** prompten, CLAUDE.md, regler och skills. Claude bestämmer själv hur den följer dem.
- **Det Claude Code tillämpar:** hooks, deny-regler och managed settings. De körs eller gäller varje gång, oavsett vad modellen bestämt.

Dokumentationen säger uttryckligen att hooks är det du använder när en åtgärd måste blockeras oavsett vad Claude beslutat. Det är den tydligaste skillnaden i kursen.

*Källa: [Memory](https://code.claude.com/docs/en/memory), avsnittet "CLAUDE.md vs auto memory": "To block an action regardless of what Claude decides, use a PreToolUse hook instead." [Hooks](https://code.claude.com/docs/en/hooks) för händelserna.*

### 2. Hook-händelser

Hooks körs vid bestämda händelser i Claude Code:s livscykel. Några av de viktigaste är:

- **`PreToolUse`**: innan ett verktyg körs. Här kan du blockera.
- **`PostToolUse`**: efter att ett verktyg körts. Den kan inte ångra det som gjorts.
- **`Stop`**: när Claude slutar en tur.
- **`SessionStart`** och **`UserPromptSubmit`**: i början av en session, och när du skickar en prompt.

Dokumentationen listar fler händelser, bland annat för underagenter, kontext och modellbyten. Det är ingen anledning att kunna dem alla utantill i den här lektionen.

*Källa: [Hooks](https://code.claude.com/docs/en/hooks), avsnittet "Hook events". Beskrivningen av vad `PostToolUse` kan och inte kan ångra är en slutsats av att verktyget redan körts, vilket dokumentationen anger i tabellen över exit code-beteenden.*

### 3. Konfigurationen

Hooks definieras i JSON-filer, till exempel `.claude/settings.json`. Strukturen har tre nivåer: en händelse, en matcher-grupp och en eller flera hook-handlers.

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "powershell -NoProfile -File .claude/hooks/block-prod-config.ps1"
          }
        ]
      }
    ]
  }
}
```

Matchern filtrerar när hooken körs. För verktygshändelser matchar den verktygets namn. En matcher med bokstäver, siffror och `|` är en exakt matchning eller en lista. Andra tecken gör den till ett reguljärt uttryck.

*Källa: [Hooks](https://code.claude.com/docs/en/hooks), avsnitten "Configuration structure" och "Matchers". Exemplet med PowerShell är kursens anpassning, inte en dokumenterad konfiguration.*

### 4. Exit code och JSON

Ett command-hook får händelsens data som JSON på stdin. Exit code avgör vad som händer:

- **Exit 0:** åtgärden fortsätter, såvida inte JSON-utdata som hooken skrev säger något annat.
- **Exit 2:** åtgärden stoppas. Meddelandet som Claude får är hookens stderr.
- **Annan exit code:** ett icke-blockerande fel. Åtgärden fortsätter.

Dokumentationen varnar uttryckligen: om en hook ska tillämpa en policy, använd `exit 2`. Exit code 1 utan giltig JSON är inte blockerande.

I `PreToolUse` kan du i stället returnera JSON med `permissionDecision` som `allow`, `deny`, `ask` eller `defer`. Exit 2 blockerar dock alltid, även om JSON säger `allow`.

*Källa: [Hooks](https://code.claude.com/docs/en/hooks), avsnitten "Exit codes" och "JSON output and PreToolUse decisions".*

### 5. Vad hooks kan och inte kan lita på

En hook körs deterministiskt när händelsen och matchern stämmer. Modellen avgör inte om den körs. Men två saker bör du känna till:

- Fältet `file_path` är den parameter som dokumentationen anger för `Edit` och `Write`, och `tool_input` följer verktygets parametrar. Vi har ändå inte sett fältet i en stdin-utskrift i kursen. Kontrollera därför fältnamnet i labbet, genom att skriva stdin till en fil innan du bygger logiken.
- En `PreToolUse`-hook som timar ut blockerar inte automatiskt. Dokumentationen varnar: förlita dig inte på en hook som stannar som grind.

Dokumentationen säger också att `@`-referenser i prompten inte passerar genom `PreToolUse`-hooks, eftersom ingen verktygsanropning sker. Om du vill styra vilka filer som läses, använd en `Read`-deny-regel.

*Källa: [Hooks](https://code.claude.com/docs/en/hooks), avsnittet "Input on stdin" och timeout-varningen. [Common workflows](https://code.claude.com/docs/en/common-workflows), avsnittet "Reference files and directories", om `@` och hooks.*

### 6. Behörigheter: allow, ask och deny

Permission rules är inställningar som avgör vilka verktyg som körs utan att fråga. De ligger också i `settings.json`, under `permissions`. Tre nivåer:

- **`allow`**: körs utan fråga.
- **`ask`**: frågar alltid.
- **`deny`**: blockeras.

Dokumentationen är tydlig med följande:

- Deny-regler blockerar i alla lägen, även i `bypassPermissions`.
- Allow-regler har ingen effekt i `bypassPermissions`.
- I auto mode utvärderas allow-, ask- och deny-regler först, före klassificeraren. En deny-regel blockerar därför även där.

En regel som blockerar läsning av `.env` skrivs `Read(./.env)`, enligt dokumentationens tabell över regelsyntax. Regler utvärderas i ordningen deny, ask och sedan allow, och den första träffen avgör. Ett bart verktygsnamn som `Bash` tar bort verktyget från Claudes kontext helt.

*Källa: [Permission modes](https://code.claude.com/docs/en/permission-modes), avsnitten "Deny rules and precedence" och "Auto mode and the classifier". Syntaxen `Read(./.env)` och evalueringsordningen finns i [Configure permissions](https://code.claude.com/docs/en/permissions), under "Rule syntax" och "Deny rules".*

### 7. Auto mode och hooks

Auto mode godkänner rutinmässiga åtgärder med en klassificerare. Hooks och deny-regler gäller före den. Det innebär att du kan kombinera dem: auto mode för att minska prompts, och hooks och deny-regler för det som aldrig får ske.

*Källa: [Permission modes](https://code.claude.com/docs/en/permission-modes), avsnittet "Auto mode and the classifier", punkt 1 i beslutsordningen.*

### 8. Underagenter

En underagent är en Markdown-fil med YAML-frontmatter som definierar en specialiserad agent. Den ligger i `.claude/agents/` för projektet, eller `~/.claude/agents/` för alla dina projekt. Bara `name` och `description` är obligatoriska.

```markdown
---
name: code-reviewer
description: Reviews code for quality and best practices
tools: Read, Glob, Grep
model: sonnet
---

You are a code reviewer. When invoked, analyze the code and provide
specific, actionable feedback on quality, security, and best practices.
```

Fältet `tools` är en allowlist. `disallowedTools` är en denylist. Fältet `model` kan vara `sonnet`, `opus`, `haiku` eller ett modell-ID.

Dokumentationen har tre inbyggda underagenter: **Explore** för snabb och skrivskyddad sökning, **Plan** för research i plan-läget, och **general-purpose** för flerstegsuppgifter.

*Källa: [Subagents](https://code.claude.com/docs/en/sub-agents), avsnitten "Subagent file locations", "Frontmatter" och "Built-in subagents".*

### 9. Underagentens kontext

Varje underagent har ett eget kontextfönster, med egen systemprompt och egna verktyg. En ny underagent ser inte din konversationshistorik, de skills du redan anropat eller de filer Claude redan läst. Det Claude skickar till den är ett delegeringsmeddelande.

Tillbaka till din session kommer bara sammanfattningen. Det är poängen: stora filläsningar stannar i underagentens fönster. Underagenter kostar också egna tokens, och förfrågningarna räknas mot samma användningsgränser.

*Källa: [Subagents](https://code.claude.com/docs/en/sub-agents), avsnittet "Context window behavior". [Explore the context window](https://code.claude.com/docs/en/context-window), avsnittet "The follow-up prompt".*

### 10. Isolering med worktrees

Ett git-worktree är en separat utcheckning på en egen gren. Du kan köra två sessioner parallellt utan att redigeringarna kolliderar:

```bash
claude --worktree feature-auth
```

Ett underagentsfält, `isolation: worktree`, kör underagenten i en tillfällig worktree. Den rensas automatiskt om underagenten inte gör några ändringar. Worktrees skapas från din standardgren, inte från din aktuella `HEAD`.

Med `claude --worktree` hamnar worktreen i `<repo>/.claude/worktrees/<namn>`. Repot måste ha minst en commit för att ett worktree ska kunna skapas.

*Källa: [Common workflows](https://code.claude.com/docs/en/common-workflows), avsnittet "Run parallel sessions with worktrees". [Subagents](https://code.claude.com/docs/en/sub-agents), fältet `isolation`.*

### 11. Vad du inte ska förlita dig på

- En CLAUDE.md-regel som "läs aldrig `.env`" är en instruktion, inte en garanti. Använd en deny-regel.
- En hook som bara skriver ut en varning har ingen effekt på beslutet. Den måste returnera exit 2 eller `deny`.
- `@`-referenser går förbi `PreToolUse`-hooks. Blockera med `Read`-deny-regler.

*Källa: [Memory](https://code.claude.com/docs/en/memory), avsnittet "CLAUDE.md vs auto memory". [Hooks](https://code.claude.com/docs/en/hooks), exit codes. [Common workflows](https://code.claude.com/docs/en/common-workflows), `@`-avsnittet.*

### Sammanfattning av genomgången

- Det Claude Code tillämpar är hooks, deny-regler och managed settings. Det Claude läser är CLAUDE.md, regler och skills.
- En hook blockerar med exit 2, eller med `permissionDecision: "deny"` i JSON.
- Deny-regler gäller i alla lägen, även i auto mode och `bypassPermissions`.
- Underagenter ligger i `.claude/agents/` och har egen kontext. Bara sammanfattningen kommer tillbaka.
- `--worktree` och `isolation: worktree` ger isolerade ändringar.

---

## Grundkommandon i den här lektionen

| Kommando | Vad det gör |
| --- | --- |
| `/hooks` | Visar hook-konfigurationerna. |
| `/permissions` | Hanterar allow-, ask- och deny-regler, och granskar auto mode-nekanden. |
| `/agents` | Skriver ut en påminnelse om hur underagenter skapas och hanteras. |
| `claude --worktree <namn>` | Startar en session i en egen worktree. |
| `claude --agent <namn>` | Startar hela sessionen med en viss underagent. |

---

## Labb (1 timme 35 minuter)

Labbet görs i ett .NET-repo. Steg 1 till 3 skapar filer i `.claude/`, så gör det i en kopia eller på en separat gren.

### Steg 1: se vad hooken får (15 min)
1. Skapa `.claude/hooks/log-stdin.ps1` med innehållet `$input | Out-File -Append -Encoding utf8 $env:TEMP\hook-stdin.json`.
2. Lägg till en `PreToolUse`-hook i `.claude/settings.json` med matchern `Edit|Write` som kör skriptet.
3. Be Claude ändra en rad i en fil, till exempel `add a one-line comment to README.md`.
4. Öppna `hook-stdin.json` i `$env:TEMP` och läs fältnamnen.

**Förväntat:** en JSON-rad med `tool_name` och `tool_input`. Anteckna exakt vad sökvägen till filen heter i `tool_input`. Om det inte är `file_path`, använd det namn du ser i nästa steg.

### Steg 2: blockera med exit 2 (20 min)
1. Skapa `.claude/hooks/block-prod-config.ps1`:
   ```powershell
   $payload = [Console]::In.ReadToEnd() | ConvertFrom-Json
   $path = $payload.tool_input.file_path
   if ($path -like '*appsettings.Production*') {
     [Console]::Error.WriteLine('Blocked: production config is not editable by Claude.')
     exit 2
   }
   exit 0
   ```
   Byt `file_path` mot det fältnamn du fann i steg 1 om det skiljer sig.
2. Byt `log-stdin.ps1` i inställningarna mot `block-prod-config.ps1`.
3. Skapa en fil `appsettings.Production.json` i kopian, och be Claude ändra den.
4. Kontrollera att Claude får blockeringsmeddelandet och att filen inte ändrats.

**Förväntat:** Claude säger att åtgärden blockerades, och `git diff` visar ingen ändring i produktionsfilen. Om hooken inte fungerar, kontrollera fältnamnet igen.

### Steg 3: deny-regel som gäller i auto mode (20 min)
1. Lägg till i `.claude/settings.json`:
   ```json
   {
     "permissions": {
       "deny": ["Read(./.env)"]
     }
   }
   ```
2. Kontrollera syntaxen i `/permissions`. Om regeln inte syns, justera den enligt det som visas där.
3. Skapa en `.env` med en fejkad hemlighet, och be Claude läsa den.
4. Byt till auto mode med `Shift+Tab` och be om samma sak igen.

**Förväntat:** Claude kan inte läsa `.env`, både i normalläge och i auto mode. Anteckna om regeln fungerade i båda lägena.

### Steg 4: skapa en underagent (20 min)
1. Skapa `.claude/agents/code-reviewer.md` med frontmatter enligt teorin: `name`, `description`, `tools: Read, Glob, Grep`.
2. Be Claude: `use the code-reviewer subagent to review the last change in this repo`.
3. Jämför med `/context`. Hur mycket lades till i din huvudsession?

**Förväntat:** en sammanfattning från underagenten i din session, och kontexten växer mindre än om Claude läst alla filerna själv.

### Steg 5: worktree (20 min)
1. Avsluta och kör i ett annat fönster: `claude --worktree labb-tva`.
2. Be Claude göra en liten ändring i en fil.
3. Kontrollera med `git worktree list` att en separat worktree skapades.
4. Kontrollera med `git status` i huvudkopian att den inte påverkats.

**Förväntat:** två separata arbetskataloger, och ändringen syns bara i worktreen.

### Steg 6: rensa upp (10 min)
1. Ta bort de tillfälliga hookarna och deny-regeln, eller använd kopian som du kastar.
2. Kör `git status` och kontrollera att arbetskatalogen är ren eller att bara kursfilerna finns kvar.

---

## Quiz och reflektion (25 min)

1. Vad är skillnaden mellan en CLAUDE.md-regel och en deny-regel?
2. Vad händer i Claude Code om en `PreToolUse`-hook avslutar med exit 2?
3. Varför gäller en deny-regel även i auto mode?
4. Vad ser den underagent som du startar av en ny session, och vad ser den inte?
5. Varför kan du inte förlita dig på en hook som bara skriver ut en varning?

**Reflektion (skriv 3 meningar):** Vilken regel i ditt projekt skulle du vilja tillämpa med en hook i stället för en instruktion? Vilket fältnamn fanns i stdin-JSON, och stämde det med det du väntade dig? Vad skulle du inte delegera till en underagent?

Facit finns i [quiz-facit.md](quiz-facit.md), som bara instruktören ska använda.

---

## Referenser

- [Hooks](https://code.claude.com/docs/en/hooks): händelser, konfiguration, exit codes, JSON-utdata.
- [Permission modes](https://code.claude.com/docs/en/permission-modes): lägen, deny-regler, auto mode.
- [Subagents](https://code.claude.com/docs/en/sub-agents): filformat, frontmatter, inbyggda agenter.
- [Common workflows](https://code.claude.com/docs/en/common-workflows): worktrees, `@`-referenser.
- [Memory](https://code.claude.com/docs/en/memory): varför CLAUDE.md inte är en garanti.

---

## Slide-outline (ungefär 25 slides för genomgången)

1. Titel: "Lektion 4: Hooks, behörigheter och underagenter".
2. Mål (fem punkter).
3. Två sorters instruktioner: tolkat och tillämpat.
4. Hook-händelser: `PreToolUse`, `PostToolUse`, `Stop`, `SessionStart`.
5. Konfiguration i tre nivåer.
6. Matcher: exakt, lista eller reguljärt uttryck.
7. Stdin: händelsens JSON.
8. Exit 0, exit 2 och andra exit codes.
9. JSON-utdata: `permissionDecision` med fyra värden.
10. Varför exit 2 blockerar även om JSON säger `allow`.
11. Vad en hook kan lita på, och vad den inte kan.
12. `@`-referenser går förbi hooks.
13. Permission rules: allow, ask, deny.
14. Deny-regler i alla lägen.
15. Beslutsordningen i auto mode.
16. Underagenter: var de ligger och vad som krävs.
17. Frontmatter: `tools`, `disallowedTools`, `model`.
18. Inbyggda agenter: Explore, Plan, general-purpose.
19. Egen kontext: vad som följer med och vad som inte gör det.
20. Worktrees med `--worktree`.
21. `isolation: worktree` i underagenter.
22. Fyra saker du inte ska förlita dig på.
23. Bild: deterministiskt mot icke-deterministiskt.
24. Labbet i korthet.
25. Innan du går vidare: de fem quizfrågorna och bedömningen.

---

## Bedömning

Lektionen är godkänd när eleven:

- [ ] kan förklara skillnaden mellan en instruktion och en hook,
- [ ] har skrivit en `PreToolUse`-hook som blockerar med exit 2, och verifierat att den fungerar,
- [ ] har kontrollerat fältnamnet i stdin-JSON själv, och inte antagit det,
- [ ] har skrivit en deny-regel och verifierat den i både normalläge och auto mode,
- [ ] har skapat en underagent och kan förklara vad den ser och inte ser,
- [ ] har kört `--worktree` och kontrollerat att huvudkopian inte påverkats,
- [ ] klarar minst 4 av 5 quizfrågor.

## Vanliga fel

- **Hooken körs inte:** matchern stämmer inte med verktygets namn, eller JSON-filen har fel struktur. Kontrollera med `/hooks`.
- **Hooken körs men blockerar inte:** hooken skriver bara ut en varning, eller returnerar exit code 1. Använd exit 2.
- **Fältet `file_path` finns inte:** namnet kan skilja sig för den händelse du matchar. Kontrollera stdin-filen från steg 1.
- **Deny-regeln syns inte i `/permissions`:** syntaxen stämmer inte. Justera enligt det som visas där.
- **Underagenten dyker inte upp:** filen ligger på fel plats, eller `name` och `description` saknas.
- **Worktree skapas inte:** repot saknar en commit. Gör en första commit i kopian.
