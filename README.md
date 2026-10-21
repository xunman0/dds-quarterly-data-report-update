# DDS Quarterly Data Report Update

A Claude skill that fills in the quarterly **DDS (Service Access and Equity) DATA report** Google Sheet for a community navigator program, using the regional center's client tracking sheet and each client's Google Drive folder.

It captures the rules and the working method developed while completing a real quarterly report, so the next quarter can be done faster and consistently.

## What it does

| Sheet area | What gets filled in | Source |
|---|---|---|
| Ethnicity (tag + text) | Ethnicity tag and description | IPP or CDER |
| Regional center services | Count, services, `RCSERV_*` tags, notes for `RCSERV_OTHER` | IPP or CDER ("Regional center funded services") |
| Generic services | Services, `GENSERV_*` tags, "Other Generic Service Notes" | IPP or CDER ("Other funded services", Benefits & Supports) |
| Health insurance and Medi-Cal | Insurer, Medi-Cal variant by aid code, Medi-Cal network group | IPP or CDER (Benefits & Supports) |
| Follow-up contacts | Count of dated log entries in the quarter | Client log |
| Barriers tab | Barrier descriptions, per-tag counts, notes for `BARRIER_OTHER` | Client log |

## Key rules at a glance

- **Sources:** IPP or CDER for ethnicity, services, insurance and Medi-Cal. The client log is used only for follow-up counts and barriers. IFSP (Early Start) plans and CDER-only files are skipped for services. Planned, declined or pending services are not entered.
- **RCOC services:** every client gets "Service Coordination / Case Management" (tag `RCSERV_OTHER`, also listed in the notes column).
- **Never write the auto-count column** (`# of Generic Services`); it is a formula.
- **Insurance:** use the specific insurer from the dropdown, or "Private Insurance" when the provider is unknown; tag `GENSERV_OTHER` and repeat the insurer in the notes column.
- **Medi-Cal:** the aid code picks the variant (DDS-W, SSI/SSP, p5.108.FPL, or plain), tag `GENSERV_MEDICAL`, and the network goes in the Medi-Cal Group column.
- **Tags:** SSI/SSDI use `GENSERV_SSI_SSDI`; any speech, OT or PT service uses `GENSERV_SPEECH_OT_PT`; IHSS, IEP and ABA have their own tags; WIC and Cal-Fresh use `GENSERV_FOODASSIST` and HUD uses `GENSERV_BASICNEEDS` (not repeated in the notes); everything else is `GENSERV_OTHER` with a note.
- **Barriers:** one line per barrier in the format `BARRIER_TAG: description;` (each ending in a semicolon, so the count formula works), with per-tag counts and a Total row below the last client.

The full rules, dropdown mappings and tag definitions are in [`SKILL.md`](SKILL.md).

## How it works

1. **Gather** the quarter's clients from the tracking sheet (filter by regional center client tag and service start date).
2. **Extract** data from each client's Drive folder using parallel sub-agents (about 12 clients each), returning one line per client.
3. **Map** the extracted items to the sheet's dropdown options and tags.
4. **Write** to the sheet by pasting tab-separated blocks (typed text is rejected by dropdown validation).
5. **Verify** by reading the sheet back as CSV and diffing against what was intended.
6. **Report** caveats: skipped clients, unmatched options, and anything uncertain.

## Requirements

- Claude with access to Google Drive, Google Sheets in a browser (Claude in Chrome), and the ability to run sub-agents.
- Edit access to the quarterly report sheet and read access to the tracking sheet and client folders.
- The program's tag list (the "Standardized Community Navigator Measures Tag List") for tag definitions.

## Installing

Copy the contents of `SKILL.md` into a new skill in your Claude account (Settings, then Skills), or save the folder where your Claude client loads skills.

## Privacy

This repository contains only rules and workflow. It holds **no client names, UCI numbers or case details**. Keep it that way: never commit exports from the sheet or the Drive folders.

## Notes and known quirks

- Some formulas in the sheet may reference the wrong row; check them before relying on totals.
- Logs for exited or transferred clients can live in different folders; search by UCI as a fallback.
- Siblings often share one folder and one log.
