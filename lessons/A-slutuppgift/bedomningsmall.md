# Bedömningsmall: Avslutande uppgift (endast för instruktören)

Använd mallen för varje elev. Varje kriterium bedöms som **uppfyllt**, **delvis** eller **ej uppfyllt**. Godkänt kräver att alla kriterier i kolumnen "Krav" är uppfyllda, och att högst ett kriterium i kolumnen "Fördjupning" är ej uppfyllt.

| Område | Krav för godkänt | Fördjupning |
| --- | --- | --- |
| Funktion | Ändringen gör det specen säger, och `dotnet build` och `dotnet test` körs med utdata redovisad. | Kanter täckta av tester som eleven själv skrev. |
| Spec | `SPEC.md` finns, med ett "klart"-kriterium som kan kontrolleras mot utdata. | Specen täcker minst tre kanter, och en av dem ändrade koden. |
| Plan | Plan-läge användes före ändringen, eller eleven kan förklara varför inte. | Eleven kan visa vad planen ändrade. |
| Bevis | Minst ett påstående från Claude kontrollerades mot utdata, och eleven kan visa hur. | Eleven kan beskriva ett fel som upptäcktes, och hur det rättades. |
| Verktyg | Minst ett av alternativen i uppgiften används, och valet motiveras. | Två eller fler alternativ används, och eleven kan jämföra dem. |
| Kontext | Eleven kan förklara vad som laddas i kontexten, och varför sessionen byttes eller togs om. | Eleven kan visa `/context` före och efter en större uppgift. |
| Kostnad | `/usage` noterades före och efter, och siffrorna redovisas ärligt. | Eleven kan peka på den dyraste delen och en konkret åtgärd. |
| Styrning | Eleven kan förklara vad som är en instruktion och vad som är en tvingande regel. | En hook eller deny-regel används för något som inte får ändras. |
| Redovisning | Redovisningen ryms i tiden, och eleven svarar på frågor utan att gissa. | Eleven säger själv var arbetet inte är verifierat. |

## Ej godkänt, och vad det beror på

- **Påstående utan utdata:** eleven säger att något fungerar utan att visa kontrollen. Det är det vanligaste och viktigaste felet i kursen. Ge eleven en chans att visa utdatan i redovisningen innan du dömer.
- **Ingen spec, eller en spec utan kriterium:** bygget saknar en mätbar målbild.
- **Oläst diff:** eleven kan inte förklara en ändring som finns i diffen. Det är ett ägarskapsfel, som kursen behandlar i lektion 0.
- **Nekat skydd:** en deny-regel eller hook har tagits bort för att få bygget att gå igenom, utan att det motiverats.

## Anteckningar

Skriv ned de två starkaste och de två svagaste punkterna, och vad eleven skulle kunna göra nästa gång. Det är underlag för den som skriver nästa kurs.
