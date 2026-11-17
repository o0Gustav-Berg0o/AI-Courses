# Facit: Lektion 2 (endast för instruktören)

1. **Vad är skillnaden mellan CLAUDE.md och auto memory?**
   CLAUDE.md skriver du, och den innehåller instruktioner och regler. Auto memory skriver Claude själv, och den innehåller lärdomar och mönster från dina rättelser. Båda laddas i varje session.

2. **Var skulle du lägga en instruktion som bara gäller dig, och inte teamet?**
   I `~/.claude/CLAUDE.md` om den gäller dig i alla projekt. I `CLAUDE.local.md` i projektroten om den bara gäller dig i det här projektet, och filen ska stå i `.gitignore`. Att lägga den i projektets CLAUDE.md vore fel, eftersom den checkas in och delas med teamet.

3. **Varför ska en CLAUDE.md hållas under ungefär 200 rader?**
   Längre filer tar mer kontext och minskar hur väl instruktionerna följs. Det som bara gäller en del av kodbasen flyttas till regler med `paths:`.

4. **Varför är CLAUDE.md inte rätt plats för regeln "läs aldrig `.env`"?**
   CLAUDE.md är kontext, inte tvingande konfiguration. Claude kan i sällsynta fall bryta en instruktion. En regel som måste gälla alltid ska vara en deny-regel eller en PreToolUse-hook, som Claude Code tillämpar oavsett modellens beslut.

5. **Vad gör en regel med `paths:` som en vanlig regel utan `paths:` inte gör?**
   Den laddas inte vid start. Den laddas först när Claude läser, skriver eller ändrar en fil som matchar mönstret. Regler utan `paths:` laddas vid start med samma prioritet som `.claude/CLAUDE.md`.

## Bedömning

Godkänt vid minst 4 av 5 rätt. Om en elev missar fråga 4 är det värt en genomgång i början av lektion 4, där hooks och deny-regler tas upp.
