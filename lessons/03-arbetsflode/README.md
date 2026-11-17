# Lektion 3: Arbetsflöde och verifiering

**Längd:** 4 timmar.
- Genomgång (läs och diskutera): 2 timmar.
- Labb: 1 timme 35 minuter.
- Quiz och reflektion: 25 minuter.

**Förkrav**
- Lektion 0, 1 och 2 är genomförda.
- Ett .NET-repo med ett test-projekt som går att köra med `dotnet test`.
- Labbet gör ändringar i kod, så gör det i en kopia eller på en separat gren.

## Mål

Efter lektionen kan du:

1. Förklara varför plan-läget är det säkraste sättet att börja med en större ändring.
2. Skriva en `SPEC.md` som Claude kan bygga efter, och förklara varför bygget startas i en ny session.
3. Be om bevis för att något fungerar, och kontrollera utdatan själv.
4. Förklara vad `/goal` gör, och vilka villkor som gör ett mål verifierbart.
5. Förklara vad `/verify` och `/code-review` gör, och vad du fortfarande måste kontrollera själv.

---

## Teori (genomgång, 2 timmar)

Varje avsnitt har en källa. Där något är en rekommendation eller en del av kursen, och inte en dokumenterad funktion, står det uttryckligen.

### 1. Planera innan du ändrar

Plan-läget låter Claude läsa filer, köra kommandon för att utforska och skriva en plan, men ändra inga källfiler. Redigeringar blockeras tills du godkänt planen. Det är det säkraste sättet att börja med en större ändring.

Du kan gå in i plan-läget på tre sätt:

- `claude --permission-mode plan` från kommandoraden,
- `Shift+Tab` i sessionen, tills statusraden visar `⏸ plan mode on`,
- `/plan` följt av en beskrivning, till exempel `/plan fix the auth bug`.

När planen är klar erbjuder Claude tre val: godkänna och använda auto mode, godkänna och godkänna redigeringar manuellt, eller fortsätta planera. Om du vill lämna plan-läget utan att godkänna trycker du `Shift+Tab`. Vid godkännandeprompten ska du trycka `Esc` först, eftersom `Shift+Tab` där godkänner planen.

*Källa: [Permission modes](https://code.claude.com/docs/en/permission-modes), avsnittet "Plan mode". [Common workflows](https://code.claude.com/docs/en/common-workflows), avsnittet "Plan before editing". Kommandot `/plan` finns i [Commands](https://code.claude.com/docs/en/commands).*

### 2. Skriv en spec, och bygg i en ny session

Kursen rekommenderar att du intervjuar Claude först, och sparar resultatet i en `SPEC.md`. Starta sedan en ny session för bygget. Planen och specen blir då det som bär över, inte hela det långa samtalet.

Varför en ny session? Lektion 1 visade att all historik följer med varje tur. En spec som bygger på ett långt utforskande samtal är ofta fylld med irrelevanta försök. En ren session börjar med just specen.

Dokumentationen är tydlig om att planen i plan-läget läses in igen efter en komprimering, så den kan bära över. Själva `SPEC.md`-filen är däremot en kursrekommendation, inte en inbyggd funktion. Den fungerar eftersom Claude kan läsa den som vilken fil som helst.

*Källa: [Best practices](https://code.claude.com/docs/en/best-practices), avsnittet "Brainstorm with Claude first". Vid komprimering återinläses planen, enligt [Explore the context window](https://code.claude.com/docs/en/context-window), avsnittet "What survives compaction". Att använda `SPEC.md` är kursens rekommendation.*

### 3. "Klart" är inte ett bevis

Det vanligaste problemet med AI-verktyg är inte att de svarar fel. Det är att de säger att de är klara utan att ha kontrollerat det. Enligt best practices beror det oftast på att Claude inte hade något sätt att kontrollera sitt eget arbete.

Därför är den viktigaste vanan att ge Claude en kontroll att köra, och be om utdatan. I ett .NET-repo kan det vara `dotnet build` och `dotnet test`. "Alla tester passerar" är ett påstående. Utdatan från `dotnet test` är ett bevis.

*Källa: [Best practices](https://code.claude.com/docs/en/best-practices), avsnittet "Give Claude a check to run": "Whichever option you use, ask Claude for evidence". Tabellen "What you can stop doing" tar upp samma sak.*

### 4. Skriv ett verifierbart mål

Det finns en mekanism som låter Claude fortsätta arbeta över flera turer tills ett villkor är uppfyllt. Det är `/goal`.

`/goal` sätter ett villkor. Efter varje tur kontrollerar en modell om villkoret är uppfyllt. Om inte, startar Claude en ny tur i stället för att lämna tillbaka kontrollen till dig. Målet rensas när villkoret är uppfyllt, när modellen bedömer att det inte går att uppfylla, eller när en tur fallerar på ett fel du måste åtgärda.

```text
/goal all tests in test/auth pass and the lint step is clean
```

Dokumentationen är tydlig med vad ett bra villkor innehåller:

- **Ett mätbart slut**: ett testresultat, en exit code, en tom kö.
- **En angiven kontroll**: hur Claude ska bevisa det, till exempel "`dotnet test` exits 0".
- **Begränsningar som spelar roll**: det som inte får ändras på vägen.

Det är en viktig begränsning: utvärderaren kör inga kommandon själv och läser inga filer. Den kan bara bedöma det Claude redan har visat i samtalet. Därför måste villkoret vara något som Claudes egen utdata kan visa.

Ett mål i `-p`-läge kör hela loopen i ett anrop. Det är ett sätt att använda kursens kommandon i CI, men det tas upp mer i lektion 5.

*Källa: [Keep Claude working toward a goal](https://code.claude.com/docs/en/goal), avsnitten "Set a goal", "Write an effective condition" och "How evaluation works". Kommandot finns i [Commands](https://code.claude.com/docs/en/commands).*

**Viktigt för kursen:** `/goal` är nytt och är inte testat i en session i kursen. Sources.md markerar det som ej verifierat i beteendet. Labbet ska göras, och resultatet antecknas.

### 5. `/verify` och `/code-review`

`/verify` är en inbyggd skill som ska bekräfta att en kodändring gör det den ska, genom att bygga och köra appen och observera resultatet, i stället för att förlita sig på tester eller typkontroll. Dokumentationen säger att den inte kräver setup för vanliga projekttyper, och att den för svårare projekt skriver ett recept till `.claude/skills/verify/SKILL.md`.

`/code-review` granskar ändringar i den aktuella diffen, eller en PR, för korrekthetsfel. Den kan med `--fix` tillämpa förslagen och med `--comment` posta dem som kommentarer på en PR. Den kan alltså skriva filer och posta på GitHub, så den ska användas med eftertanke.

Båda är verktyg som hjälper dig att granska. Ingen av dem ersätter att du själv läser diffen.

*Källa: [Skills](https://code.claude.com/docs/en/skills), avsnittet "Run and verify your app". [Commands](https://code.claude.com/docs/en/commands), tabellerna för `/verify` och `/code-review`. Beteendet i en session är ej verifierat i kursen, enligt sources.md.*

### 6. Granska diffen själv

Det som gör verifieringen trovärdig är att du läser ändringen. Kör `git diff` efter varje steg, och kontrollera att det som ändrats är det du bad om. Om något ändrats utanför det du bad om, säg till, eller använd `/rewind`.

Kom ihåg begränsningen från lektion 1: `/rewind` återställer inte ändringar som gjorts med Bash. Git är det säkerhetsnät som täcker allt.

*Källa: [Best practices](https://code.claude.com/docs/en/best-practices), avsnittet "Steer the session". Begränsningen om Bash står i samma avsnitt.*

### 7. Hur de här delarna hänger ihop

En lämplig arbetsgång för en större ändring:

1. **Intervjua och skriv spec.** Spara i `SPEC.md`.
2. **Plan-läge.** Låt Claude läsa koden och skriva en plan. Godkänn eller justera.
3. **Ny session för bygget.** Peka på specen, och ge en kontroll att köra.
4. **Mål eller tur för tur.** Antingen ett `/goal` med mätbart villkor, eller manuella turer med kontroll efter varje.
5. **Granska diffen och utdatan.** Kontrollera själv, även om `/code-review` gjort en genomgång.

Det här är en rekommendation från kursen. Dokumentationen beskriver delarna var för sig, men inte som en färdig metod.

*Källa: Kursens rekommendation, byggd av delarna ovan. Varje del har sin källa i avsnitten 1 till 6.*

### 8. Auto mode och mål

Auto mode godkänner rutinmässiga verktygsanrop utan att fråga, och en klassificerare granskar varje åtgärd. Men auto mode startar inte nya turer. Den godkänner anrop inom en tur, och Claude slutar när den bedömer att arbetet är klart. `/goal` lägger till en separat utvärderare som kontrollerar villkoret efter varje tur. Det är två olika saker som kompletterar varandra.

Ett mål ändrar inte din permission mode. Om du vill att målets turer ska gå utan att fråga, kör `/goal` i auto mode.

*Källa: [Keep Claude working toward a goal](https://code.claude.com/docs/en/goal), avsnittet "Compare ways to keep a session running" och "Set a goal". Klassificeraren beskrivs i [Permission modes](https://code.claude.com/docs/en/permission-modes).*

### 9. Vanliga missförstånd

- **"Claude sa att det fungerar, alltså fungerar det."** Nej. Begär utdatan från kontrollen.
- **"`/goal` kör kommandon för mig."** Nej. Utvärderaren kör inga kommandon. Claude måste köra dem och visa utdatan i samtalet.
- **"En spec ska vara lång och detaljerad."** Specen ska vara tillräcklig för att en ny session kan bygga den. Den behöver inte beskriva hur koden ska se ut.
- **"`/code-review` ersätter min egen granskning."** Nej. Det är ett extra par ögon, inte ett godkännande.

*Källa: [Best practices](https://code.claude.com/docs/en/best-practices), avsnitten "Give Claude a check to run" och "Brainstorm with Claude first". [Keep Claude working toward a goal](https://code.claude.com/docs/en/goal), avsnittet "How evaluation works".*

### Sammanfattning av genomgången

- Plan-läge först för större ändringar. Redigeringar blockeras tills planen är godkänd.
- Skriv en spec i `SPEC.md`, och bygg i en ny session.
- Be alltid om bevis, till exempel utdatan från `dotnet test`.
- `/goal` kräver ett villkor som Claudes egen utdata kan visa.
- `/verify` och `/code-review` hjälper dig granska, men ersätter inte din läsning av diffen.
- Git är ditt säkerhetsnät. `/rewind` täcker inte Bash-ändringar.

---

## Grundkommandon i den här lektionen

| Kommando | Vad det gör |
| --- | --- |
| `claude --permission-mode plan` | Startar en session i plan-läge. |
| `Shift+Tab` | Växlar permission mode, till plan-läge bland annat. |
| `/plan [beskrivning]` | Går in i plan-läge och startar direkt på uppgiften. |
| `/goal <villkor>` | Sätter ett mål som Claude arbetar mot över flera turer. |
| `/goal` | Visar status för aktivt eller tidigare uppnått mål. |
| `/goal clear` | Rensar ett aktivt mål. |
| `/verify` | Bygger och kör appen för att bekräfta en ändring. |
| `/code-review [--fix] [--comment] [pr#/branch/path]` | Granskar diffen eller en PR för korrekthetsfel. |

---

## Labb (1 timme 35 minuter)

Labbet görs i ett .NET-repo med ett test-projekt. Steg 3 och 4 ändrar kod, så gör dem i en kopia eller på en separat gren, och kontrollera att arbetskatalogen är ren först.

### Steg 1: plan-läge och en spec (20 min)
1. Starta en session med `claude --permission-mode plan`.
2. Be Claude intervjua dig om en liten funktion, till exempel: `Interview me with questions one at a time until we can write a spec for adding a request-id header to every HTTP response. Then write SPEC.md.`
3. Svara ärligt, och lägg till en kant som du vill ha täckt, till exempel felhantering.
4. Läs `SPEC.md` när den skrivits. Kontrollera att den har ett tydligt "klart"-kriterium.

**Förväntat:** en `SPEC.md` med ett kontrollerbart "klart"-kriterium, och inga ändrade källfiler. Kontrollera med `git status`.

### Steg 2: ny session och kontroll (15 min)
1. Avsluta sessionen med `/exit`.
2. Starta en ny session med `claude`, och be Claude läsa `SPEC.md`.
3. Be Claude först visa vilket kommando som bevisar att arbetet fungerar, och vad det förväntas ge.

**Förväntat:** Claude nämner ett kommando, till exempel `dotnet test`, och ett förväntat resultat. Anteckna om det var det du förväntade dig.

### Steg 3: bygg med bevis (25 min)
1. Be Claude implementera specen, med kravet att den kör kontrollen och visar utdatan.
2. Granska `git diff` innan du godkänner något.
3. Kontrollera själv utdatan från kontrollen i terminalen. Lita inte bara på Claudes sammanfattning.

**Förväntat:** en ändring och utdata från kontrollen. Om utdatan säger något annat än Claude hävdade, är det lektionens viktigaste observation.

### Steg 4: mål med villkor (25 min)
1. Börja från en gren där något testfall inte passerar. Om alla passerar, lägg in ett enkelt testfall som misslyckas.
2. Kör `/goal` med ett mätbart villkor, till exempel: `dotnet test exits 0 and no test file is modified. Stop after 10 turns.`
3. Följ målets turer. Notera hur många som kördes, och vad utvärderaren angav som skäl.
4. Efter att målet rensats, kör `/goal` utan argument och läs statusen.

**Förväntat:** antingen att målet uppnåddes med utdata från `dotnet test`, eller att det avbröts med en förklaring. Båda är lärande. Anteckna om utvärderarens skäl stämde med utdatan.

Om `/goal` inte fungerar som beskrivs, anteckna det. Det är en observation för sources.md.

### Steg 5: granska med `/code-review` (15 min)
1. Kör `/code-review` utan argument, på den aktuella diffen.
2. Jämför förslagen med din egen läsning av `git diff`.
3. Kör inte `--fix` eller `--comment` i labbet.

**Förväntat:** en lista med förslag. Anteckna minst ett förslag som var korrekt och ett som var fel eller onödigt.

### Steg 6: rensa upp (15 min)
1. Kasta ändringen i kopian: `git checkout -- .`, bara i kopian.
2. Kontrollera med `git status` att arbetskatalogen är ren.

**Förväntat:** en ren arbetskatalog.

---

## Quiz och reflektion (25 min)

1. Varför är plan-läget ett bra första steg för en större ändring?
2. Varför startar kursen bygget i en ny session i stället för i samma session som specen skrevs i?
3. Vad måste ett bra `/goal`-villkor innehålla?
4. Varför kan `/goal` inte bekräfta att tester passerar om Claude inte visar utdatan?
5. Vad är skillnaden mellan auto mode och `/goal`?

**Reflektion (skriv 3 meningar):** Vilken påstådd "klar"-status från Claude visade sig vara fel när du kontrollerade den? Vad skulle ha hänt om du inte kontrollerat? Vilket villkor skulle du skriva om, så att det blev mer kontrollerbart?

Facit finns i [quiz-facit.md](quiz-facit.md), som bara instruktören ska använda.

---

## Referenser

- [Permission modes](https://code.claude.com/docs/en/permission-modes): plan-läge, auto mode, Shift+Tab.
- [Common workflows](https://code.claude.com/docs/en/common-workflows): plan före redigering, worktrees.
- [Keep Claude working toward a goal](https://code.claude.com/docs/en/goal): `/goal`, villkor, utvärdering.
- [Best practices](https://code.claude.com/docs/en/best-practices): ge Claude en kontroll att köra, be om bevis.
- [Skills](https://code.claude.com/docs/en/skills): `/verify` och andra bundlade skills.
- [Commands](https://code.claude.com/docs/en/commands): `/plan`, `/goal`, `/code-review`, `/verify`.

---

## Slide-outline (ungefär 25 slides för genomgången)

1. Titel: "Lektion 3: Arbetsflöde och verifiering".
2. Mål (fem punkter).
3. Planera innan du ändrar: plan-läget.
4. Tre sätt in i plan-läget.
5. Vad plan-läget blockerar, och vad det tillåter.
6. Godkännandeprompten: tre val, och varför `Esc` först.
7. Spec i `SPEC.md`: intervjun.
8. Varför bygget startar i en ny session.
9. "Klart" är inte ett bevis.
10. Ge Claude en kontroll att köra.
11. Bild: påstående mot utdata.
12. `/goal`: villkor, kontroll efter varje tur.
13. Tre delar av ett bra villkor.
14. Utvärderaren kör inga kommandon. Claude måste visa utdatan.
15. `/verify`: bygg och kör appen.
16. `/code-review`: granska, med `--fix` och `--comment` med eftertanke.
17. Granska diffen själv.
18. `/rewind` och Bash-ändringar.
19. Arbetsgången i fem steg.
20. Auto mode och `/goal`: två olika saker.
21. Fyra vanliga missförstånd.
22. Vad som är verifierat och vad som ännu är ej verifierat i kursen.
23. Sammanfattning: plan, spec, bevis, mål, granskning.
24. Labbet i korthet.
25. Innan du går vidare: de fem quizfrågorna och bedömningen.

---

## Bedömning

Lektionen är godkänd när eleven:

- [ ] kan förklara varför plan-läget är ett bra första steg,
- [ ] har skrivit en `SPEC.md` med ett kontrollerbart "klart"-kriterium,
- [ ] har byggt i en ny session, och bett Claude visa kontrollen först,
- [ ] har kontrollerat utdatan från kontrollen själv, inte bara lita på sammanfattningen,
- [ ] har kört `/goal` med ett mätbart villkor, och antecknat resultatet,
- [ ] kan förklara varför `/goal` kräver att utdatan visas i samtalet,
- [ ] har kört `/code-review` utan `--fix` och `--comment`, och jämfört förslagen med sin egen läsning,
- [ ] klarar minst 4 av 5 quizfrågor.

## Vanliga fel

- **Plan-läget ändrar ingenting, men Claude frågar om kommandon:** i plan-läget kan vissa kommandon kräva godkännande. Det är förväntat, och svara i sessionen.
- **Godkännandeprompten försvann när jag tryckte `Shift+Tab`:** vid godkännandeprompten godkänner `Shift+Tab` planen. Tryck `Esc` först.
- **`/goal` stannar utan att klara villkoret:** utvärderaren ser inte utdatan. Be Claude köra kontrollen och visa resultatet, och kör `/goal` igen.
- **`/goal` fortsätter för länge:** lägg till en gräns i villkoret, till exempel "stop after 10 turns".
- **`/code-review` hittar inget eller för mycket:** den ersätter inte din läsning. Jämför med diffen själv.
