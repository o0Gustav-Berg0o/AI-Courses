# Facit: Lektion 6 (endast för instruktören)

1. **Varför ska en organisations regel som måste gälla alltid vara en hook eller en deny-regel?**
   CLAUDE.md är kontext som Claude tolkar, inte tvingande konfiguration. En hook eller deny-regel tillämpas av Claude Code oavsett modellens beslut, så den är det enda som garanterar att regeln gäller.

2. **Kan en utvecklare exkludera organisationens managed CLAUDE.md med `claudeMdExcludes`?**
   Nej. Utvecklare kan exkludera andra CLAUDE.md-filer, men inte den managed CLAUDE.md.

3. **Varför växer kostnaden i en lång session även när svaren är korta?**
   Varje tur skickar hela konversationen på nytt. Den cachade delen är rabatterad bara så länge cachen är varm. Gamla orelaterade meddelanden kostar därför i varje tur, även när de inte behövs för svaret.

4. **Vad visar uppdelningen i `/usage` per MCP-server?**
   Hur mycket av användningen som gått till den servern. Den visar var kostnaden kommer ifrån, så att du kan granska en server som står för en stor del.

5. **Varför är auto mode inte ett skyddsnät i sig?**
   Dokumentationen säger: "Auto mode reduces permission prompts but does not guarantee safety." Klassificeraren är en granskning, men den garanterar inte att allt är säkert. Deny-regler och hooks ger de hårda gränserna.

## Bedömning

Godkänt vid minst 4 av 5 rätt. Om en elev missar fråga 1 eller 2 är det värt att repetera skillnaden mellan styrning som tolkas och styrning som tillämpas.

Notera för instruktören: facit bygger på den lästa dokumentationen för managed settings, `autoMode` och `claudeMdExcludes`. Fråga 2 gäller `claudeMdExcludes`, och fråga 1 gäller managed settings och hooks.
