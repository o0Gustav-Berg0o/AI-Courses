# Källor

Verifierade den 2026-11-17. Alla länkar går till den officiella dokumentationen för Claude Code på code.claude.com.

## Lästa i sin helhet

| Sida | Används i | Anteckning |
| --- | --- | --- |
| [Best practices](https://code.claude.com/docs/en/best-practices) | Lektion 1, 2, 3, 5, 6 | Hela sidan läst. Källan till "Give Claude a check", "Enforce the rules", "Let Claude run without you" och "What you can stop doing". |
| [Hur Claude kommer ihåg ditt projekt (Memory)](https://code.claude.com/docs/en/memory) | Lektion 2, 6 | Hela sidan läst. CLAUDE.md-platser, auto memory-gränser, AGENTS.md. |
| [Explore the context window](https://code.claude.com/docs/en/context-window) | Lektion 0, 1 | Vad som laddas, vad som överlever `/compact`. |
| [Quickstart](https://code.claude.com/docs/en/quickstart) | Lektion 0 | Installation per operativsystem, första sessionen, grundkommandon. |

## Lästa delvis eller via sammanfattning

| Sida | Används i | Anteckning |
| --- | --- | --- |
| [Hooks](https://code.claude.com/docs/en/hooks) | Lektion 4 | Händelselistan och JSON-formatet verifierade. Övriga detaljer ej läst. |
| [Subagents](https://code.claude.com/docs/en/sub-agents) | Lektion 4 | Platser, filformat, inbyggda agenter verifierade. |
| [Common workflows](https://code.claude.com/docs/en/common-workflows) | Lektion 0, 3, 4, 5 | Plan-läge, worktrees, återupptagning, headless verifierade. |
| [Commands](https://code.claude.com/docs/en/commands) | Alla | Alla slash-kommandon i kursen finns i referensen. |
| [CLI reference](https://code.claude.com/docs/en/cli-reference) | Lektion 0, 5 | Alla flaggor i kursen finns. Accepterade värden för `--permission-mode` och `--output-format` verifierade. |

## Ej verifierat ännu

- `/verify`, `/goal` och `/code-review`: finns i kommandoreferensen, men beteendet är inte testat i en session. Kontrolleras i labben.
- Exempel med `dotnet format` och `dotnet build` som hook: vår anpassning, inte från dokumentationen.
- Auto mode-konfigurationen (`autoMode` i `~/.claude/settings.json`): läst i best practices, ej testad.
