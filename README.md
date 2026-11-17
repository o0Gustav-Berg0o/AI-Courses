# AI kurser

Kurser för nyexaminerade .NET-utvecklare om att arbeta med AI-verktyg i modern systemutveckling.

## Kurs 1: Kodning med Claude Code

**Mål:** efter kursen kan eleven installera och använda Claude Code i ett riktigt .NET-projekt, styra kontext och minne, verifiera Claudes arbete, automatisera med hooks och underagenter, och köra Claude utan person i loopen på ett säkert sätt.

**Format:** 6 lektioner à 4 timmar, plus en avslutande uppgift på 4 timmar. Totalt cirka 28 timmar.

**Ram för varje lektion:**

| Del | Tid |
| --- | --- |
| Genomgång (text, läses eller gås igenom) | 2 h |
| Labb | 1 h 35 min |
| Quiz och reflektion | 25 min |

Tiderna summerar till 4 timmar utan buffert. Lektion 0 är skriven efter den här ramen. Lektionerna 1 till 6 ska följa samma ram när de skrivs ut.

## Lektionsplan

| Nr | Lektion | Huvudkommandon och koncept | Status |
| --- | --- | --- | --- |
| 0 | [Installation och första sessionen](lessons/00-installation/README.md) | `claude`, `claude -p`, `claude -c`, `/context`, `Shift+Tab` | Fullt utskriven |
| 1 | [Kontext, sessioner och kostnad](lessons/01-kontext/README.md) | `/context`, `/compact`, `/clear`, `/rewind`, `/usage` | Fullt utskriven |
| 2 | [CLAUDE.md och minne](lessons/02-minne/README.md) | `/init`, `/doctor`, `/memory`, `CLAUDE.local.md`, `.claude/rules/` | Fullt utskriven |
| 3 | [Arbetsflöde och verifiering](lessons/03-arbetsflode/README.md) | plan-läge, `SPEC.md`, `/goal`, `/verify`, `/code-review` | Fullt utskriven |
| 4 | [Hooks, behörigheter och underagenter](lessons/04-hooks-och-underagenter/README.md) | hooks, `permissions.deny`, `.claude/agents/`, `--worktree` | Fullt utskriven |
| 5 | [Headless och automation](lessons/05-headless/README.md) | `claude -p`, `--allowedTools`, `--permission-prompts`, `--bare`, `/loop`, `/schedule` | Fullt utskriven |
| 6 | [Team, styrning och kostnad](lessons/06-team-och-styrning/README.md) | managed CLAUDE.md, `claudeMdExcludes`, `/usage`, auto mode-konfiguration | Fullt utskriven |
| A | [Avslutande uppgift](lessons/A-slutuppgift/README.md) | En funktion från början till slut | Fullt utskriven |

## Lektionsmallen

Varje lektion ska ha samma delar, så att de går att jämföra:

1. **Mål**: vad eleven kan efter lektionen.
2. **Teori**: korta punkter, med källa för varje påstående.
3. **Labb**: steg med tidsbudget och förväntat resultat.
4. **Quiz**: fem frågor, med facit i en separat fil för instruktören.
5. **Referenser**: länkar till officiell dokumentation.
6. **Slide-outline**: en lista över slides.
7. **Bedömning**: checklista som avgör om lektionen är godkänd.

## Källor

Alla påståenden om Claude Code ska gå att kontrollera i den officiella dokumentationen. Se [sources.md](sources.md) för länkar och vad som verifierats.

## Status

- Verifierat mot officiell dokumentation den 2026-11-17.
- Lektion 0 till 6 och den avslutande uppgiften är fullt utskrivna, enligt lektionsmallen.
- Lektion 3, 4, 5 och 6 innehåller påståenden som inte är testade i en session. De är markerade i respektive lektion och i [sources.md](sources.md).
- Inga slide-filer finns i repot ännu. Slide-outlines finns i varje lektion.

## Struktur

```text
AI kurser/
  README.md                  kursplan och status
  sources.md                 verifierade källor
  lessons/
    00-installation/
      README.md              lektion 0, komplett
      quiz-facit.md          facit för instruktören
    01-kontext/
      README.md              lektion 1, komplett
      quiz-facit.md          facit för instruktören
    02-minne/                lektion 2, komplett (samma filer)
    03-arbetsflode/          lektion 3, komplett (samma filer)
    04-hooks-och-underagenter/  lektion 4, komplett (samma filer)
    05-headless/             lektion 5, komplett (samma filer)
    06-team-och-styrning/    lektion 6, komplett (samma filer)
    A-slutuppgift/
      README.md              avslutande uppgift
      bedomningsmall.md      bedömning för instruktören
```
