# Avslutande uppgift: En funktion från början till slut

**Längd:** 4 timmar.
- Uppgift: 3 timmar 30 minuter.
- Redovisning: 30 minuter.

Den avslutande uppgiften har ingen quiz. Redovisningen och bedömningen ersätter den. Instruktören använder [bedömningsmallen](bedomningsmall.md).

**Förkunskap**
- Lektion 0 till 6 är genomförda.
- Ett .NET-repo där du kan skapa en ny funktion i en gren, och köra `dotnet build` och `dotnet test`.

## Mål

Efter uppgiften kan du:

1. Ta en funktion från en vag önskan till en verifierad ändring, med hjälp av alla delar från kursen.
2. Visa bevis för att funktionen fungerar, med utdata du själv kontrollerat.
3. Motivera vilka delar av Claude Code du använde, och varför.
4. Beskriva kostnaden för arbetet, och vad du skulle göra annorlunda.

## Uppgiften

Du bygger en liten funktion i ett .NET-repo som du väljer tillsammans med instruktören. Funktionen ska vara liten nog att bli klar på 3 timmar 30 minuter, men tillräckligt stor för att kräva en plan, ett test och en granskning.

Förslag på funktioner (välj en, eller föreslå en egen):

- Ett endpoint i ett befintligt API som returnerar en sida av resultat, med validering av parametrar.
- En klass som räknar ut något med en regel som måste testas, till exempel rabatter, avrundning eller datumlogik.
- En loggningsfunktion som skriver ett strukturerat fel när en uppgift misslyckas.

### Vad du levererar

1. **En `SPEC.md`** med ett tydligt "klart"-kriterium och minst tre kanter som ska täckas, till exempel tomma värden eller gränsvärden. Skriv den i plan-läge, enligt lektion 3.
2. **En CLAUDE.md-rad eller en regel** som styr hur tester ska köras i repot, enligt lektion 2. Den ska vara konkret och verifierbar.
3. **En ändring i kod och tester**, som du granskat själv i `git diff`. Utdata från `dotnet test` ska finnas med i redovisningen.
4. **Minst en av följande**, som du motiverar:
   - en `PreToolUse`-hook eller en deny-regel som skyddar något som inte får ändras, enligt lektion 4,
   - en underagent som gör en undersökning utan att fylla huvudsessionen, enligt lektion 4,
   - ett `/goal` med ett mätbart villkor som du följt, enligt lektion 3,
   - ett headless-skript som kör testerna med `--bare` och `--allowedTools`, enligt lektion 5.
5. **En kostnadsnotering** från `/usage` i början och i slutet av arbetet, och en mening om vad som var dyrast.
6. **En redovisning på högst 10 minuter** där du visar utdatan och svarar på frågor.

### Så arbetar du

Det här är en rekommendation från kursen. Du kan avvika, men motivera det.

1. **Skapa en gren.** Arbeta aldrig direkt på huvudgrenen.
2. **Intervjua och skriv spec.** Plan-läge, och spara `SPEC.md`.
3. **Ny session för bygget.** Ge Claude en kontroll att köra innan den börjar.
4. **Bygg i små steg.** Granska diffen efter varje steg. Använd `/rewind` eller `git` när något går fel.
5. **Verifiera med utdata.** Kör `dotnet build` och `dotnet test` och spara utdatan.
6. **Granska själv.** Läs hela diffen innan du säger att arbetet är klart.

## Redovisning (30 minuter)

Redovisningen ska innehålla:

- **Demo (5 min):** visa funktionen fungera, med kommandot och utdatan.
- **Spec och plan (5 min):** visa `SPEC.md` och förklara en kant som ändrade koden.
- **Bevis (5 min):** visa `dotnet test`-utdatan, och förklara en gång då Claude påstod något som du fick kontrollera.
- **Verktyg (5 min):** vilket av alternativen i punkt 4 du valde, och varför.
- **Kostnad och reflektion (5 min):** `/usage` före och efter, vad som var dyrast, och vad du skulle göra annorlunda.
- **Frågor (5 min):** instruktören ställer frågor om vad du kontrollerade och varför.

## Vad som bedöms

Se [bedömningsmallen](bedomningsmall.md) för de exakta kriterierna. Kortfattat:

- Funktionen fungerar, och det finns utdata som visar det.
- Specen har ett kontrollerbart "klart"-kriterium.
- Eleven har kontrollerat påståenden själv, och kan visa när det gjordes.
- Eleven kan motivera valen av verktyg, och förklara vad som gäller och inte gäller.
- Kostnadsnoteringen är ärlig, även när den visar något oväntat.

## Slide-outline (ungefär 10 slides för redovisningen)

1. Titel: "Avslutande uppgift: [funktionens namn]".
2. Målet med funktionen, i en mening.
3. Specen: "klart"-kriteriet och tre kanter.
4. Plan-läget: vad planen ändrade.
5. Bevis: `dotnet test`-utdatan.
6. Ett tillfälle då Claude hade fel, och hur du upptäckte det.
7. Valt verktyg och varför.
8. Kostnad före och efter.
9. Vad du skulle göra annorlunda.
10. Frågor.

## Bedömning

Uppgiften är godkänd när eleven:

- [ ] levererar en fungerande ändring med utdata från `dotnet build` och `dotnet test`,
- [ ] levererar en `SPEC.md` med ett kontrollerbart "klart"-kriterium,
- [ ] visar minst ett tillfälle då påståenden kontrollerades mot utdata,
- [ ] motiverar minst ett verktyg från kursen med en källa eller en observation,
- [ ] levererar en ärlig kostnadsnotering från `/usage`.

Mer detaljerade kriterier finns i [bedomningsmallen.md](bedomningsmall.md).
