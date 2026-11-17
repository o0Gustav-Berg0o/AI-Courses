# Facit: Lektion 5 (endast för instruktören)

1. **Vad händer med en åtgärd som kräver godkännande i `claude -p`, om inget annat godkänner den?**
   Den nekas. I `-p`-körningar finns ingen person som svarar, så åtgärden blockeras om inte en `PermissionRequest`-hook, `--permission-prompt-tool`, en allow-regel eller ett läge godkänner den.

2. **Vilket utdataformat ger dig `total_cost_usd`, och varför är det en uppskattning?**
   `--output-format json`. Värdet är en klientuppskattning och kan skilja sig från den faktiska fakturan. Den faktiska kostnaden syns i `/usage` och på kontot.

3. **Vad är skillnaden mellan `--allowedTools "Bash"` och `--allowedTools "Bash(git diff *)"`?**
   Den första tillåter alla skalkommandon. Den andra tillåter bara kommandon som börjar med `git diff`. I en auto mode-körning tas dock ett bart `Bash` bort som bred allow-regel.

4. **Varför rekommenderas `--bare` för skript och CI?**
   Det hoppar över automatisk inläsning av hooks, skills, subagenter, plugins, MCP-servrar, auto memory och CLAUDE.md. Resultatet blir detsamma på varje maskin, och ingen oväntad hook körs från någon annans konfiguration.

5. **Vad måste en schemalagd prompt innehålla, som en interaktiv prompt inte behöver?**
   Den måste säga vad framgång är och vad som ska göras med resultatet. En schemalagd uppgift kan inte ställa följdfrågor.

## Bedömning

Godkänt vid minst 4 av 5 rätt. Om en elev missar fråga 1 eller 4 är det värt att repetera, eftersom det är kärnan i den här lektionen.

Notera för instruktören: `--max-turns`, `--max-budget-usd` och `--permission-prompts` är verifierade mot CLI-referensen och headless-dokumentationen.
