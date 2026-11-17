# Facit: Lektion 3 (endast för instruktören)

1. **Varför är plan-läget ett bra första steg för en större ändring?**
   I plan-läget läser Claude filer och skriver en plan, men ändrar inga källfiler. Redigeringar blockeras tills planen är godkänd. Du granskar riktningen innan något ändras på disk.

2. **Varför startar kursen bygget i en ny session i stället för i samma session som specen skrevs i?**
   Ett långt utforskande samtal fylls av försök och irrelevanta detaljer, som följer med varje tur och kostar. En ny session börjar med bara specen och planen, så bygget blir fokuserat. Det är kursens rekommendation.

3. **Vad måste ett bra `/goal`-villkor innehålla?**
   Ett mätbart slut, en angiven kontroll som visar att slutet nåtts, och de begränsningar som inte får brytas på vägen. Ett exempel är "`dotnet test` exits 0 and no test file is modified".

4. **Varför kan `/goal` inte bekräfta att tester passerar om Claude inte visar utdatan?**
   Utvärderaren kör inga kommandon och läser inga filer själv. Den bedömer bara det som Claude redan visat i samtalet. Utan utdatan i samtalet har utvärderaren inget att bedöma mot.

5. **Vad är skillnaden mellan auto mode och `/goal`?**
   Auto mode godkänner verktygsanrop inom en tur utan att fråga, men startar inte nya turer, och Claude slutar när den själv bedömer att arbetet är klart. `/goal` startar nya turer efter varje tur tills en separat utvärderare bedömer att villkoret är uppfyllt. De kompletterar varandra.

## Bedömning

Godkänt vid minst 4 av 5 rätt. Om en elev missar fråga 4 är det värt att repetera skillnaden mellan påstående och bevis, eftersom det är kursens kärna.

Notera för instruktören: `/goal` och `/code-review` är dokumenterade, men deras beteende i en session är inte testat i kursen. Labbets resultat ska föras in i sources.md.
