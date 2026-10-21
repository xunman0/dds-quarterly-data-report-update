---
name: dds-quarterly-data-report-update
description: Update the Illumination Institute DDS quarterly DATA report sheet (ethnicity, RCOC services, generic services, insurance/Medi-Cal, follow-ups) from the RCOC Share Tracking sheet and each client's Drive IPP/CDER/client log.
---

# DDS quarterly DATA report update

Use when updating the quarterly sheet "DDS Illumination INST <year> - <n>rd-qtr DATA report", tab like '3rd Quarter-MAIN - July to SEPT' (header in row 2, data from row 3). Source: read-only "RCOC Share Tracking <year>" sheet, tab "RCOC'S CLIENTS" (filter by RCOC client tag and POS start date in the quarter; paste static values). Each UCI in the tracking sheet links to the client's Drive folder ("<Client> <UCI> <worker>") holding face sheet, IPP, CDER, client log, progress reports.

## Source rules
- Use only the IPP or CDER for ethnicity, services, insurance and Medi-Cal. Use the most recent IPP. IFSP (Early Start) plans are outdated: skip. Clients with no IPP (CDER only) are skipped for services. Client logs are used only for follow-ups. Planned, declined or pending services are NOT entered.
- Read packets with the Google Drive connector. Fan out subagents (~10-14 UCIs each) returning one line per UCI.

## Columns (A=UCI ... T)
- E/F ethnicity tag + text (from IPP/CDER). I count, J RCOC services, K RCSERV tags, L notes for RCSERV_OTHER (from "Regional center funded services"). Default for EVERY client: always include "Service Coordination / Case Management" in J (column I is an auto-count formula: never write I), tag RCSERV_OTHER in K, and list it in L (Other Service(s) notes) together with any other services that map to RCSERV_OTHER.
- B Age = age on the program start date (7/1 of the quarter) from the DOB on the face sheet/IPP/CDER. G Language = the client's own preferred language. E/F ethnicity: face sheet "Hispanic, Latino/a/x, or Spanish" -> ETH_5_HISPANIC / Spanish / Latin; "Asian"/"Other Asian" -> ETH_5_ASIAN / Other; face sheet "Missing Information" -> ETH_5_OTHER_UNKNOWN / Other. Never infer ethnicity from names or narrative; leave blank and report when no document states it. Column D is ignored. Column I is an auto-count formula (never write it).
- N = auto-count formula. NEVER write column N.
- O Generic services, P Generic tag, Q Other Generic Service Notes, R Medi-Cal Group.
- T follow-up contacts = count of dated entries in the client log within the quarter (each dated entry = one interaction).

## Generic services rules (O/P/Q/R)
- Health insurance (Benefits & Supports): choose specific insurer from the dropdown (Aetna; Anthem / Anthem Blue Cross; Blue Cross / Blue Shield / BlueCross HMO; Cigna; Kaiser / Kaiser HMO / Kaiser PPO; United Health / United Health Care; CHAMPVA (Vet); Optum Health). Use "Private Insurance" when the provider is not named or not in the list (HMO/PPO with no provider). Tag GENSERV_OTHER and also put the insurer in Q. Replace a generic "Private Insurance" if the IPP names the insurer.
- Medi-Cal: aid code 6V/6W -> "Medi-Cal (DDS-W)"; 60 -> "Medi-Cal (SSI/SSP)"; P5 -> "Medi-Cal (p5.108.FPL)"; other/unknown -> "Medi-Cal". Tag GENSERV_MEDICAL. Put the network in R: CHOC Health Alliance -> CHOC; CalOptima* -> CalOptima; AMVI -> AMVI Care Medical Group; Monarch -> Monarch; United Care -> United Care; ARTA -> ARTA Western Health Network; Kaiser -> Kaiser; AltaMed -> AltaMed Medical Group; unlisted or missing network -> unknown.
- Other services: IHSS -> "In-Home Supportive Services (IHSS)" GENSERV_IHSS; SSI -> "SSI – Supplemental Security Income", SSA-DAC, SSA-Minor Child all tag GENSERV_SSI_SSDI and are NOT put in Q; school IEP -> "K–12 School / School District – IEP" GENSERV_IEP; school speech/OT -> "SELPA – Language/Speech Therapy" / "SELPA – Occupational Therapy"; private/insurance/Medi-Cal/CCS speech, OT, PT, feeding/food therapy -> matching option, tag GENSERV_SPEECH_OT_PT (any speech, OT or PT service uses this tag and is NOT put in Q); ABA -> "Behavioral Health Service / ABA" GENSERV_BEHAVIOR; WIC (Food Assist) and Cal-Fresh (Food Assist) -> GENSERV_FOODASSIST and NOT put in Q; HUD / HUD Voucher -> GENSERV_BASICNEEDS and NOT put in Q; Child Support, CCS, nursing, counseling, assistive technology, insurers -> GENSERV_OTHER and also listed in Q. Drop GENSERV_OTHER when Q ends up empty. Merge with existing cell values; do not delete existing entries.
- Multi-values are comma+space separated.

## Writing to the sheet (Claude in Chrome)
- Rows with data validation (original rows up to ~85) reject typed text. Instead select the top-left cell via the Name box (type ref, Return) then dispatch a synthetic paste: build a TSV (columns O,P,Q,R; no quotes/newlines) and run in the page: `const el=document.activeElement; const dt=new DataTransfer(); dt.setData('text/plain', tsv); el.dispatchEvent(new ClipboardEvent('paste',{clipboardData:dt,bubbles:true,cancelable:true}))`. Paste whole contiguous blocks, using current values for unchanged rows.
- Verify by fetching the gviz CSV (`/gviz/tq?tqx=out:csv&sheet=<tab>`, credentials include) and diffing against the expected TSV. Row = CSV index + 2; the last row has no trailing newline.
- If the tab group is lost, run tabs_context_mcp with createIfEmpty and reopen the sheet (MAIN tab gid=2043653857).

## Barriers tab (Barriers to services)
Tab "Barriers": A UCI, B barriers text, C Have Barriers?, D count (formula: semicolons in B, `=IF(A5="",0,LEN(B5)-LEN(SUBSTITUTE(B5,";","")))` - do not modify, but make sure each row references its own row), E STATUS, F Total tag count (`=sum(H:S)`), G NOTES, H-S tag counts, T "NOTES for OTHER Barrier". Same UCI order as the MAIN tab. Keep a Total row BELOW the last client (sums D, F, H-S).
- Tag definitions come from the "Standardized Community Navigator Measures Tag List" (Measures 16, 29) in the project: BARRIER_RCSTAFF_COM (lack of communication with regional center staff), BARRIER_INFO (information not provided in an understandable way), BARRIER_PROVIDER_COM (lack of communication with service provider staff), BARRIER_RCSTAFF_LANG, BARRIER_PROVIDER_LANG (language barriers), BARRIER_BASICNEEDS (family must focus on basic needs), BARRIER_TRANSPORT, BARRIER_SERVICES (not sure what services are available), BARRIER_DENIAL (not sure what to do if a service is denied), BARRIER_SCHEDULE (busy schedules), BARRIER_CHILDCARE, BARRIER_OTHER (describe in notes). The DDS grant guidelines PDF gives no further definitions.
- Source: client log only (IPP pass is separate, when requested). Only dated entries inside the quarter. Only barriers the parent or client experienced (not the worker's own outreach difficulty). Be conservative. IHSS/SSA/Medi-Cal/county staff issues and reimbursement delays or waitlists are usually BARRIER_OTHER; unanswered RC service coordinator = RCSTAFF_COM.
- Column B format: `BARRIER_TAG: short description; BARRIER_TAG: short description;` - each barrier ends with a semicolon (so D equals the barrier count). No semicolons, tabs or quotes inside descriptions.
- H-S: count per tag (blank for zero). T: descriptions of the BARRIER_OTHER barriers, separated by semicolons.
- Fan out subagents (~12 UCIs each) over clients that have dated log entries in the quarter; some logs sit in other folders (exited/transferred), so fall back to full-text search by UCI. Sibling pairs share logs.
- Write by synthetic paste of TSV blocks (B3 for column B; H3 for H-T), then verify with the gviz CSV for the tab (gid in URL).

## Report back
List caveats: networks marked unknown, clients skipped (no IPP / IFSP only / folder not found), items not entered (Medicare, SSDI, planned services), unmatched dropdown options.
