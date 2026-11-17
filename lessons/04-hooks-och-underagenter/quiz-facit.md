# Facit: Lektion 4 (endast för instruktören)

1. **Vad är skillnaden mellan en CLAUDE.md-regel och en deny-regel?**
   CLAUDE.md-regeln är kontext som Claude läser och tolkar, och den kan i sällsynta fall brytas. En deny-regel tillämpas av Claude Code och blockerar oavsett vad modellen beslutat.

2. **Vad händer i Claude Code om en `PreToolUse`-hook avslutar med exit 2?**
   Åtgärden stoppas. Meddelandet som Claude får är hookens stderr. Exit 2 blockerar även om JSON-utdata säger `allow`.

3. **Varför gäller en deny-regel även i auto mode?**
   I auto mode utvärderas allow-, ask- och deny-regler först, före klassificeraren. En deny-regel avgörs därför innan klassificeraren får se åtgärden. Den gäller också i `bypassPermissions`.

4. **Vad ser den underagent som du startar av en ny session, och vad ser den inte?**
   Den ser sitt eget delegeringsmeddelande, sin egen systemprompt och sina egna verktyg. Den ser inte din konversationshistorik, de skills du redan anropat eller de filer Claude redan läst.

5. **Varför kan du inte förlita dig på en hook som bara skriver ut en varning?**
   En utskrift ändrar inte beslutet. Åtgärden fortsätter om hooken inte returnerar exit 2 eller en `deny`-decision. En varning är inte en grind.

## Bedömning

Godkänt vid minst 4 av 5 rätt. Om en elev missar fråga 2 eller 3 är det värt en genomgång i början av lektion 5, eftersom headless-körningar bygger på samma regelordning.

Notera för instruktören: `file_path`-fältet i `tool_input` för `Edit` och `Write` är kursens antagande. Låt eleverna verifiera det i steg 1.
