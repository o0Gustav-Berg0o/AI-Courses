# Lektion 1: Kontext, sessioner och kostnad

**Längd:** 4 timmar.
- Genomgång (läs och diskutera): 2 timmar.
- Labb: 1 timme 35 minuter.
- Quiz och reflektion: 25 minuter.

**Förkrav**
- Lektion 0 är genomförd: Claude Code är installerat, du är inloggad och har kört `claude`, `claude -p` och `claude -c`.
- Ett .NET-repo som du kan läsa i. Labbet gör vissa ändringar, så det ska vara ett repo där du kan köra `git`.
- Git for Windows installerat, eller WSL.

## Mål

Efter lektionen kan du:

1. Förklara vad som ligger i kontextfönstret innan du har skrivit något.
2. Använda `/context` för att se vad som fyller fönstret och varför.
3. Förklara varför en lång session kostar mer än kortare sessioner med samma resultat.
4. Använda `/compact` med en inriktning, och förklara vad som överlever en komprimering.
5. Välja mellan `/clear`, `/compact`, `/rewind` och att fortsätta i samma session.
6. Återuppta en tidigare konversation med `/resume`.
7. Kontrollera kostnaden med `/usage` och förklara vad siffrorna betyder.

---

## Teori (genomgång, 2 timmar)

Varje avsnitt har en källa. Där något är en rekommendation och inte en dokumenterad funktion står det uttryckligen.

### 1. Kontextfönstret innan du skriver något

Kontextfönstret är allt Claude kan se just nu: dina instruktioner, filer den läst, egna svar och verktygsresultat. Redan innan du skriver din första prompt är en del av det fyllt. Enligt dokumentationen laddas systemprompten, auto memory, miljöinformation, namnen på MCP-verktygen, beskrivningarna av skills och dina CLAUDE.md-filer.

Det här betyder att en ny session aldrig är helt tom. Hur mycket som laddas beror på ditt projekt. En stor CLAUDE.md eller många MCP-servrar tar plats i varje session, även när du bara ställer en enkel fråga.

*Källa: [Explore the context window](https://code.claude.com/docs/en/context-window), avsnittet "What the timeline shows" och tabellen med tokenexempel. Siffrorna i tabellen är illustrativa.*

### 2. Varje tur skickar hela konversationen igen

Det viktigaste att förstå om kostnad: Claude läser inte bara din senaste fråga. Varje förfrågan skickar hela konversationen på nytt. En session med tre orelaterade uppgifter läses därför in igen i varje tur, även när du arbetar med den fjärde.

Dokumentationen säger att detta är rabatterat så länge prompt-cachen är varm, och fullpris när den har gått ut. Cachen varar ungefär en timme på ett abonnemang och fem minuter på en API-nyckel, molnleverantör eller usage credits.

*Källa: [Best practices](https://code.claude.com/docs/en/best-practices), avsnittet "Keep cost and context down". [Prompt caching](https://code.claude.com/docs/en/prompt-caching) för cachetiderna.*

### 3. Mäta: `/context` och `/usage`

Två kommandon ger svar på olika frågor:

- **`/context`** visar vad som fyller fönstret just nu, som ett färgat rutnät per kategori. Det ger också optimeringsförslag. `/context all` expanderar uppdelningen.
- **`/usage`** visar sessionens kostnad, användningsgränser på ditt abonnemang och aktivitetsstatistik. På Pro, Max, Ultra, Team och Enterprise delas användningen upp på skills, underagenter, plugins och MCP-servrar. Aliasen `/cost` och `/stats` gör samma sak.

Dokumentationen rekommenderar `/usage` som första ställe att titta när kostnaden överraskar. `/context` är första stället när svaren verkar konstiga.

*Källa: [Commands](https://code.claude.com/docs/en/commands), tabellerna för `/context`, `/usage` och `/cost`. [Best practices](https://code.claude.com/docs/en/best-practices), avsnittet "Keep cost and context down".*

### 4. Automatisk komprimering och `/compact`

När fönstret börjar bli fullt komprimerar Claude Code konversationen automatiskt. Den sammanfattar historiken och ersätter de gamla meddelandena med sammanfattningen. Sessionen fortsätter, så ett fullt fönster avslutar den inte.

Du kan agera före den automatiska komprimeringen med `/compact`, och ge det en inriktning:

```text
/compact focus on the auth bug fix
```

Då styr du vad sammanfattningen behåller, i stället för att den automatiska komprimeringen gissar vad som är viktigt.

*Källa: [Explore the context window](https://code.claude.com/docs/en/context-window), avsnittet "When your context fills up". [Commands](https://code.claude.com/docs/en/commands), tabellen för `/compact [instructions]`.*

### 5. Vad som överlever en komprimering

Efter en komprimering är det inte allt som försvinner. Dokumentationen listar vad som laddas om:

| Mekanism | Efter komprimering |
| --- | --- |
| Systemprompt och output style | Gäller fortfarande. |
| Projektets CLAUDE.md och regler utan `paths:` | Läses in från disk igen. |
| Auto memory | Läses in från disk igen. |
| Planen från plan-läget | Läses in från disk igen. |
| Regler med `paths:` och CLAUDE.md i underkataloger | Laddas igen när Claude arbetar med en fil som matchar. |
| Filer som Claude läst eller ändrat | Upp till fem, de senast ändrade, läses om. |
| Skill-innehåll | Läses in igen, med tak på 5 000 tokens per skill och 25 000 totalt. |
| Instruktioner som du bara gav i chatten | Sammanfattas. Det kan försvinna i detalj. |

Konsekvensen är att det som måste överleva ska stå i en fil, inte i chatten. Det är en rekommendation som följer av tabellen, inte en egen regel i dokumentationen. Det är också ämnet för lektion 2.

*Källa: [Explore the context window](https://code.claude.com/docs/en/context-window), avsnittet "What survives compaction".*

### 6. `/clear`, `/resume` och `claude -c`

`/clear` startar en ny konversation med tom kontext. Det raderar inte din tidigare konversation. Du kan namnge den du lämnar, och sedan återuppta den:

```text
/clear oauth-migration
/resume
```

`/clear` har aliasen `/reset` och `/new`. `/resume` öppnar en väljare, och du kan också ange ett namn eller ett ID. `/resume` har aliasen `/continue`.

Dokumentationen säger att `/compact` är rätt när du vill behålla samma konversation och bara frigöra utrymme. `/clear` är rätt mellan olika uppgifter.

*Källa: [Commands](https://code.claude.com/docs/en/commands), tabellerna för `/clear`, `/resume` och `/compact`. [Best practices](https://code.claude.com/docs/en/best-practices), avsnittet "Keep cost and context down".*

### 7. `Esc`, `/rewind` och när du ska backa

Det finns tre sätt att påverka en session som redan körs:

- **`Esc`** stoppar Claude mitt i en åtgärd och behåller det som redan gjorts. Du kan styra om utan att förlora arbete.
- **`/rewind`**, eller `Esc` två gånger på en tom prompt, öppnar en meny där du kan gå tillbaka till ett tidigare läge: koden, konversationen eller båda. Menyn kan också sammanfatta en del av konversationen när den blivit inaktuell. Aliasen `/checkpoint` och `/undo` gör samma sak.
- **`/clear`** startar om. Dokumentationen rekommenderar det om du har korrigerat samma sak två gånger. En tredje korrigering lägger bara till i en konversation som redan innehåller två misslyckade försök.

En viktig begränsning: checkpoints täcker de filer Claude ändrat med sina egna filverktyg. En fil som bara ändrats av ett Bash-kommando återställs inte. Git är fortfarande ditt säkerhetsnät.

*Källa: [Best practices](https://code.claude.com/docs/en/best-practices), avsnittet "Steer the session". [Commands](https://code.claude.com/docs/en/commands), tabellen för `/rewind`.*

### 8. Underagenter håller stora läsningar borta

En underagent har ett eget kontextfönster. När du ber Claude att låta en underagent undersöka något, stannar de stora filläsningarna i underagentens fönster. Tillbaka till din session kommer bara sammanfattningen.

Det gör underagenter bra för efterforskning som läser många filer, och mindre bra för små uppgifter. Underagenter kostar också egna tokens, så det är en avvägning. Det är inte en gratislösning.

*Källa: [Explore the context window](https://code.claude.com/docs/en/context-window), avsnittet "As Claude works" och "The follow-up prompt". [Best practices](https://code.claude.com/docs/en/best-practices), avsnittet "Keep cost and context down".*

### 9. Kostnad i praktiken

Dokumentationen är tydlig med att kontextfönstret sällan tar slut. Det är kostnaden och fokus som är skälen att hantera det. Här är en praktisk tumregel. Det är en rekommendation, inte en dokumenterad regel:

| Situation | Det rimliga steget |
| --- | --- |
| Ny, orelaterad uppgift | `/clear`, och namnge den gamla sessionen om du vill återkomma. |
| Samma uppgift, fönstret har vuxit | `/compact` med en inriktning, till exempel vad som ska behållas. |
| Fel riktning två gånger | `/rewind`, eller `/clear` och en ny prompt. |
| Stor efterforskning | Be om en underagent, så att filläsningarna stannar utanför din session. |
| Kostnaden överraskar | `/usage`, och sedan `/context` för att se vad som fyller fönstret. |

Dokumentationen nämner också bakgrundsarbete som kostar utan att du skriver: ett `/loop`, ett mål som kontrolleras efter varje tur, och underagenter. De skickar förfrågningar även när du inte skriver något.

*Källa: [Best practices](https://code.claude.com/docs/en/best-practices), avsnittet "Keep cost and context down".*

### 10. Vanliga missförstånd

- **"Eftersom fönstret är en miljon tokens behöver jag inte tänka på kontext."** Dokumentationen säger att du sällan kommer att få slut på utrymmet. Men kostnaden är ett eget skäl, och en fokuserad kontext ger ofta bättre svar. Därför är `/clear` mellan uppgifter fortfarande värt det.
- **"`/compact` och `/clear` är samma sak."** Nej. `/compact` behåller samma konversation i sammanfattad form. `/clear` börjar om med tom kontext.
- **"`/rewind` ångrar allt."** Nej. Bash-ändringar återställs inte. Git är fortfarande ditt säkerhetsnät.
- **"Komprimering är ofarligt, allt finns kvar."** Delvis. Projektets CLAUDE.md och auto memory läses in igen, men detaljerade instruktioner som bara stod i chatten sammanfattas och kan tappas.

*Källa: [Best practices](https://code.claude.com/docs/en/best-practices), tabellen "What you can stop doing" och avsnittet "Keep cost and context down". [Explore the context window](https://code.claude.com/docs/en/context-window), avsnittet "What survives compaction".*

### Sammanfattning av genomgången

- Kontextfönstret fylls redan innan du skriver. `/context` visar vad som ligger där.
- Varje tur skickar hela konversationen igen. Därför kostar gamla orelaterade uppgifter pengar.
- `/usage` visar kostnaden, `/context` visar fönstret.
- `/compact` med en inriktning styr vad som behålls. Det som måste överleva ska stå i en fil.
- `/clear` för nya uppgifter, `/resume` för att gå tillbaka.
- `Esc` och `/rewind` för att backa. Bash-ändringar följer inte med.
- Underagenter håller stora läsningar borta från din session, till en egen kostnad.

---

## Grundkommandon i den här lektionen

| Kommando | Vad det gör |
| --- | --- |
| `/context` | Visar vad som fyller kontextfönstret, med optimeringsförslag. `/context all` expanderar. |
| `/usage` | Visar sessionens kostnad, användningsgränser och aktivitet. Aliasen `/cost` och `/stats`. |
| `/compact [inriktning]` | Sammanfattar konversationen för att frigöra utrymme. Tar emot en inriktning. |
| `/clear [namn]` | Startar en ny konversation med tom kontext. Namnet märker den gamla i `/resume`. |
| `/resume [session]` | Återupptar en tidigare konversation med ID, namn eller via väljaren. |
| `/rewind` | Går tillbaka till ett tidigare läge för kod, konversation eller båda. |
| `Esc` | Stoppar Claude mitt i en åtgärd. |
| `Esc` två gånger | Öppnar rewind-menyn när prompten är tom. |

---

## Labb (1 timme 35 minuter)

Labbet görs i ett .NET-repo. Steg 4 och 5 ändrar filer, så gör dem i en kopia eller på en separat gren, som i lektion 0. Kontrollera att arbetskatalogen är ren med `git status` innan du börjar.

### Steg 1: startläget (10 min)
1. Starta `claude` i repot.
2. Skriv `/context` utan att skriva något annat.
3. Anteckna de tre största posterna i rutnätet och hur många tokens de tar.

**Förväntat:** en lista över kategorier, till exempel systemprompt, CLAUDE.md, MCP-verktyg och skills. Du har en baslinje att jämföra med senare.

### Steg 2: se hur kontexten växer (15 min)
1. Ställ tre frågor som kräver att Claude läser filer, till exempel: `where is the main service defined and what does it depend on?`
2. Kör `/context` igen.
3. Jämför med baslinjen från steg 1. Vilken kategori växte mest?

**Förväntat:** att fönstret har vuxit, och att filläsningar syns som poster i listan. Skriv ned vilken fil som tog mest plats.

### Steg 3: komprimera med en inriktning (15 min)
1. Kör `/compact` med en inriktning, till exempel: `/compact keep the names of the files we read and the dependencies you found`.
2. Kör `/context` igen.
3. Ställ en fråga som bara går att besvara med det som behållits, till exempel `which file did you say the service depends on?`

**Förväntat:** fönstret är mindre efter komprimeringen. Svaret på följdfrågan stämmer med koden. Om det inte stämmer, anteckna vad som gick förlorat, och vilken del av inriktningen som saknades.

### Steg 4: rewind efter en ändring (15 min)
1. Be Claude lägga till en kommentar på en rad i en fil med verktygen för filändring, till exempel `add a one-line comment to the main service file`.
2. Kör `/rewind` och välj att återställa koden till läget före ändringen.
3. Kontrollera med `git status` och `git diff`.
4. Be nu Claude att använda Bash för att lägga till en rad i en annan fil, till exempel `use bash to append a line to README.md`.
5. Kör `/rewind` igen och återställ koden. Kontrollera filen.

**Förväntat:** steg 3 är rent. I steg 5 finns Bash-ändringen kvar, eftersom checkpoints inte täcker Bash. Det är lektionens observation, och det som ska föras till reflektionen.

### Steg 5: byt uppgift med `/clear` och återuppta (15 min)
1. Kör `/clear labb-lektion-1`.
2. Ställ en orelaterad fråga, till exempel `what does this repo's test project target?`
3. Kör `/exit`, och starta sedan `claude -c`. Vilken konversation öppnas?
4. Kör `/resume` och välj `labb-lektion-1`.

**Förväntat:** efter `/clear` har den nya sessionen inget av det tidigare. `claude -c` återupptar den senaste konversationen i mappen, som är den nya. `/resume` hittar den märkta gamla konversationen.

### Steg 6: se kostnaden (10 min)
1. Kör `/usage`.
2. Anteckna sessionens kostnad, och om det finns en uppdelning, vilken del som tog mest.
3. Om du inte har ett abonnemang som visar uppdelning, anteckna vad som visas i stället.

**Förväntat:** en siffra för sessionen och en bild av var användningen gick. Om uppdelningen saknas är det också en observation.

### Steg 7: låt en underagent undersöka (15 min)
1. Be Claude: `use a subagent to find every place in this repo where a class named X is defined` (byt X mot ett klassnamn i repot).
2. Kör `/context` före och efter, eller jämför med baslinjen från steg 1.
3. Jämför hur mycket som tillkommit i din huvudsession med hur mycket filläsningar som krävdes.

**Förväntat:** sammanfattningen i din session är kortare än det underagenten läste. Det är poängen med underagenter.

---

## Quiz och reflektion (25 min)

1. Vad visar `/context`, och vad är skillnaden mot `/usage`?
2. Vad är skillnaden mellan `/clear` och `/compact`?
3. Vilka delar av kontexten överlever en komprimering utan att du gör något?
4. Varför kostar en session med tre orelaterade uppgifter mer än tre separata sessioner, när svaret på den fjärde är detsamma?
5. Varför återställer inte `/rewind en fil som ändrats av ett Bash-kommando?

**Reflektion (skriv 3 meningar):** Vilken kategori i `/context` tog mest plats i ditt repo, och vad tror du att den innehåller? Vilket steg i labbet gav ett annat resultat än du väntade dig? Hur skulle du ändra din egen användning av `/clear` efter labbet?

Facit finns i [quiz-facit.md](quiz-facit.md), som bara instruktören ska använda.

---

## Referenser

- [Explore the context window](https://code.claude.com/docs/en/context-window): vad som laddas, vad som överlever komprimering.
- [Best practices](https://code.claude.com/docs/en/best-practices): kostnad och kontext, att styra sessionen.
- [Commands](https://code.claude.com/docs/en/commands): `/context`, `/compact`, `/clear`, `/resume`, `/rewind`, `/usage`.
- [Checkpointing](https://code.claude.com/docs/en/checkpointing): vad rewind täcker och inte täcker.
- [Prompt caching](https://code.claude.com/docs/en/prompt-caching): när cachen varar.
- [Sub-agents](https://code.claude.com/docs/en/sub-agents): underagenter och deras egna fönster.

---

## Slide-outline (ungefär 25 slides för genomgången)

1. Titel: "Lektion 1: Kontext, sessioner och kostnad".
2. Mål (sju punkter).
3. Kontextfönstret innan du skriver: vad som laddas.
4. Bild: startläget i ett rutnät.
5. Varje tur skickar hela konversationen igen.
6. Prompt-cachen: rabatt när den är varm, fullpris när den gått ut.
7. `/context`: vad fönstret innehåller.
8. `/usage`: kostnad, gränser och uppdelning per skill, underagent, plugin och MCP-server.
9. Automatisk komprimering: vad som händer när fönstret fylls.
10. `/compact` med en inriktning.
11. Tabell: vad som överlever en komprimering.
12. Vad som bara står i chatten och kan försvinna.
13. `/clear`: ny början, utan att den gamla försvinner.
14. `/resume` och `claude -c`: hur du går tillbaka.
15. Namnge sessioner med `/clear namn`.
16. `Esc`: stoppa och styr om.
17. `/rewind`: koden, konversationen eller båda.
18. Bash-ändringar följer inte med vid rewind.
19. Tre gånger fel: `/clear` och en ny prompt.
20. Underagenter: egen kontext, egen kostnad.
21. Tabell: vad du gör i vilken situation.
22. Bakgrundsarbete som kostar utan att du skriver.
23. Fyra vanliga missförstånd.
24. Sammanfattning: `/context` för fönstret, `/usage` för pengarna.
25. Innan du går vidare: de fem quizfrågorna och bedömningen.

---

## Bedömning

Lektionen är godkänd när eleven:

- [ ] kan förklara vad som laddas i kontextfönstret innan första prompten,
- [ ] har använt `/context` före och efter arbete och kan peka på vad som ökade,
- [ ] kan förklara varför en lång session med orelaterade uppgifter kostar mer,
- [ ] har kört `/compact` med en inriktning och kontrollerat att den behållit det viktiga,
- [ ] kan förklara skillnaden mellan `/clear`, `/compact` och `/rewind`,
- [ ] har återupptagit en namngiven session med `/resume`,
- [ ] kan förklara varför `/rewind` inte återställer Bash-ändringar,
- [ ] har kört `/usage` och kan beskriva vad sessionens siffror betyder,
- [ ] klarar minst 4 av 5 quizfrågor.

## Vanliga fel

- **`/resume` visar ingen konversation:** du startade i en annan mapp. Konversationer är knutna till mappen de startades i.
- **`/rewind` återställde inte allt:** ändringen gjordes med Bash. Kontrollera med `git diff` och återställ manuellt.
- **`/context` visar mycket i början:** CLAUDE.md, auto memory eller många MCP-servrar. Det är inte fel, men det kostar i varje tur. Lektion 2 handlar om just det.
- **`/usage` visar inte uppdelning:** uppdelningen gäller Pro, Max, Ultra, Team och Enterprise. Med andra inloggningssätt visas en annan bild.
- **Svaret efter komprimering är fel:** instruktionen stod bara i chatten. Lägg den i en fil, som lektion 2 visar.
