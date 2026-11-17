# Facit: Lektion 1 (endast för instruktören)

1. **Vad visar `/context`, och vad är skillnaden mot `/usage`?**
   `/context` visar vad som fyller kontextfönstret just nu, som ett färgat rutnät per kategori, med optimeringsförslag. `/usage` visar sessionens kostnad, användningsgränser och aktivitet. `/context` svarar på "vad ligger i fönstret", `/usage` på "vad kostar det".

2. **Vad är skillnaden mellan `/clear` och `/compact`?**
   `/compact` sammanfattar samma konversation och frigör utrymme, och den går att fortsätta. `/clear` startar en ny konversation med tom kontext. Den gamla finns kvar och kan återupptas med `/resume` om den namngetts.

3. **Vilka delar av kontexten överlever en komprimering utan att du gör något?**
   Systemprompten och output style, projektets CLAUDE.md och regler utan `paths:`, auto memory, planen från plan-läget, upp till fem nyligen ändrade filer, och skill-innehåll med tak. Instruktioner som bara stod i chatten sammanfattas och kan tappas.

4. **Varför kostar en session med tre orelaterade uppgifter mer än tre separata sessioner, när svaret på den fjärde är detsamma?**
   Varje förfrågan skickar hela konversationen på nytt. I en lång session läses de tre gamla uppgifterna in igen i varje tur, även när de inte behövs. Det är rabatterat så länge prompt-cachen är varm och fullpris när den gått ut. Separata sessioner, eller `/clear` mellan uppgifterna, undviker det.

5. **Varför återställer inte `/rewind` en fil som ändrats av ett Bash-kommando?**
   Checkpoints täcker bara de filer Claude ändrat med sina egna filverktyg i huvudkonversationen. Bash-ändringar spåras inte. Git är det säkerhetsnät som också täcker dem.

## Bedömning

Godkänt vid minst 4 av 5 rätt. Om en elev missar fråga 4 eller 5 är det värt en genomgång i början av nästa lektion, eftersom kostnad och checkpoints återkommer i lektion 4 och 5.
