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
| 1 | Kontext, sessioner och kostnad | `/context`, `/compact`, `/clear`, `/rewind`, `/usage` | Disposition, se nedan |
| 2 | CLAUDE.md och minne | `/init`, `/doctor`, `/memory`, `CLAUDE.local.md`, `.claude/rules/` | Disposition, se nedan |
| 3 | Arbetsflöde och verifiering | plan-läge, `SPEC.md`, `/goal`, `/verify`, `/code-review` | Disposition, se nedan |
| 4 | Hooks, behörigheter och underagenter | hooks, `permissions.deny`, `.claude/agents/`, `--worktree` | Disposition, se nedan |
| 5 | Headless och automation | `claude -p`, `--allowedTools`, `--max-turns`, `/loop`, `/schedule` | Disposition, se nedan |
| 6 | Team, styrning och kostnad | managed settings, `claudeMdExcludes`, `/usage`, auto mode-konfiguration | Disposition, se nedan |
| A | Avslutande uppgift | En funktion från början till slut | Disposition, se nedan |

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
- Lektion 0 är fullt utskriven, med en tvåtimmars textgenomgång (13 avsnitt), labb och quiz.
- Lektionerna 1 till 6 och avslutningen är dispositioner. De är inte utskrivna ännu.
- Inga slide-filer finns i repot ännu. Slide-outlines finns i varje lektion.
- Ingen commit är gjord.

## Struktur

```text
AI kurser/
  README.md                  kursplan och status
  sources.md                 verifierade källor
  lessons/
    00-installation/
      README.md              lektion 0, komplett
      quiz-facit.md          facit för instruktören
```
