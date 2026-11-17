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

## Lästa för lektion 1 till 6 (2026-11-17)

| Sida | Används i | Anteckning |
| --- | --- | --- |
| [Explore the context window](https://code.claude.com/docs/en/context-window) | Lektion 1, 3 | Läst: laddning, komprimering, vad som överlever. |
| [Best practices](https://code.claude.com/docs/en/best-practices) | Lektion 1 till 6 | Läst: kostnad, `/clear`, "Steer the session", "Give Claude a check". |
| [Commands](https://code.claude.com/docs/en/commands) | Lektion 1 till 6 | Läst: `/context`, `/compact`, `/clear`, `/rewind`, `/usage`, `/init`, `/memory`, `/doctor`, `/loop`, `/schedule`, `/permissions`, `/plan`, `/code-review`, `/verify`. |
| [Memory](https://code.claude.com/docs/en/memory) | Lektion 2, 4, 6 | Läst: CLAUDE.md-platser, laddning, `paths:`, `@`-import, auto memory (200 rader / 25 KB), managed CLAUDE.md, `claudeMdExcludes`. |
| [Hooks](https://code.claude.com/docs/en/hooks) | Lektion 4, 6 | Läst: händelser, konfiguration, exit codes, `permissionDecision`. Fältet `file_path` i `tool_input` för `Edit` och `Write` är ej verifierat; kontrolleras i labbet. |
| [Subagents](https://code.claude.com/docs/en/sub-agents) | Lektion 4 | Läst: platser, frontmatter, inbyggda agenter, `isolation: worktree`, kontext. |
| [Permission modes](https://code.claude.com/docs/en/permission-modes) | Lektion 3, 4, 5, 6 | Läst: lägen, plan-läge, Shift+Tab, auto mode-klassificeraren, deny-regler. |
| [Run Claude Code programmatically](https://code.claude.com/docs/en/headless) | Lektion 5, 6 | Läst: `-p`, utdataformat, `--allowedTools`, `--bare`, `--permission-prompts`, `--max-budget-usd`, bakgrundsarbete. |
| [Common workflows](https://code.claude.com/docs/en/common-workflows) | Lektion 3, 4, 5 | Läst: plan före redigering, worktrees (`--worktree`), schemalagda körningar, `@`-referenser. |
| [Keep Claude working toward a goal](https://code.claude.com/docs/en/goal) | Lektion 3, 6 | Läst: `/goal`, utvärdering, villkor. Beteendet i en session är ej testat. |
| [Skills](https://code.claude.com/docs/en/skills) | Lektion 2, 3 | Läst delvis: `/verify` och `/doctor`. Bundlade skills. |

## Ej verifierat ännu

- Beteendet för `/goal`, `/verify` och `/code-review` i en session: dokumentationen är läst, men ingen session har kört dem. Labben i lektion 3 ska ge svaret.
- Exempel med `dotnet format` och `dotnet build` som hook: vår anpassning, inte från dokumentationen.
- PowerShell-hookexemplen i lektion 4: vår anpassning. Fältnamnet `file_path` kontrolleras i labbet.
- Syntaxen `Read(./.env)` för deny-regler: dokumentationen nämner `Read`-deny-regler, men exemplet i lektion 4 är vårt.
- `--max-turns`: står i CLI-referensen enligt tidigare verifiering, men är inte läst i sin helhet för lektion 5.
- Managed settings: sidan finns, men innehållet är inte läst i sin helhet för lektion 6.
- Auto mode-konfigurationen (`autoMode`, `/auto-mode-setup`): läst delvis i permission modes och commands, inte i sin helhet.
- `claudeMdExcludes`-syntaxen: beskriven i memory, men exempel i lektion 6 är ej testat.
