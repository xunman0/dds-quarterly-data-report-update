# Generic Services Reference

A reference for filling the **Generic Services** columns of the quarterly DDS Service Access and Equity (SAE) DATA report. It maps each generic (non-regional-center) service a client receives to its `GENSERV_*` tag.

The main file, [`Generic-services-and-tags.md`](Generic-services-and-tags.md), is written for AI agents. This README explains what it contains and how to use it.

## What it covers

| Sheet column | Header | What gets filled |
|---|---|---|
| N | # of Generic Services | Auto-count formula (never written) |
| O | Generic Services | Service names from the dropdown |
| P | Generic Service Tag | One or more `GENSERV_*` tags |
| Q | Other Generic Service Notes | Services tagged `GENSERV_OTHER` only |
| R | Medi-Cal Group | Medi-Cal health network |

## Quick rules

- **Source:** the client's most recent IPP or CDER only. No IFSP plans, client logs or progress reports. No planned, pending or declined services.
- **Specific tag first:** if a service has its own tag, use it and do **not** repeat the service in the notes column.
- **`GENSERV_OTHER` + notes:** only for services with no specific tag. The service name goes in column Q.
- **Empty notes:** if column Q ends up empty, drop `GENSERV_OTHER`.
- **Medi-Cal:** the aid code picks the variant (6V/6W DDS-W, 60 SSI/SSP, P5 p5.108.FPL, otherwise plain). Tag `GENSERV_MEDICAL`. The network goes in column R.
- **Health insurance:** pick the insurer from the dropdown, or "Private Insurance" when it is not named. Tag `GENSERV_OTHER` and also write the insurer in Q.
- **Therapies:** any speech, OT or PT service (including feeding or food therapy) uses `GENSERV_SPEECH_OT_PT`.
- **Benefits:** SSI, SSA-DAC and SSA-Minor Child use `GENSERV_SSI_SSDI`. WIC and Cal-Fresh use `GENSERV_FOODASSIST`. HUD uses `GENSERV_BASICNEEDS`.

## Tag cheat sheet

| Tag | Typical services |
|---|---|
| `GENSERV_MEDICAL` | Medi-Cal (all variants) |
| `GENSERV_IHSS` | In-Home Supportive Services |
| `GENSERV_SSI_SSDI` | SSI, SSA-DAC, SSA-Minor Child |
| `GENSERV_IEP` | School IEP |
| `GENSERV_SPEECH_OT_PT` | Speech, OT, PT, audiology, feeding therapy (private, insurance, CCS or school) |
| `GENSERV_BEHAVIOR` | Behavioral health / ABA |
| `GENSERV_FOODASSIST` | WIC, Cal-Fresh |
| `GENSERV_BASICNEEDS` | HUD housing / voucher |
| `GENSERV_JOB` | CalWORKs, job training |
| `GENSERV_OTHER` | Health insurers, child support, CCS, nursing, counseling, assistive technology, medical assessments |

Other available tags: `GENSERV_TRANSPORT`, `GENSERV_PARENTWS`, `GENSERV_COMMHEALTH`, `GENSERV_MENTAL`, `GENSERV_LEGALASSIST`, `GENSERV_POSTEDU`.

## Example

A client with Medi-Cal (SSI/SSP) through CHOC, SSI, a school IEP and school speech therapy:

| Column | Value |
|---|---|
| O | Medi-Cal (SSI/SSP), SSI – Supplemental Security Income, K–12 School / School District – IEP, SELPA – Language/Speech Therapy |
| P | GENSERV_MEDICAL, GENSERV_SSI_SSDI, GENSERV_IEP, GENSERV_SPEECH_OT_PT |
| Q | *(empty)* |
| R | CHOC |

## How it is used

1. Read the client's IPP or CDER (Benefits & Supports, Other funded services).
2. Map each item using the lookup tables in `Generic-services-and-tags.md`.
3. Write the values to the sheet by pasting tab-separated rows (dropdown cells reject typed text).
4. Verify by reading the sheet back as CSV and comparing it with what was intended.
5. Report anything unmapped, any Medi-Cal network marked "unknown", and any client skipped for lack of an IPP/CDER.

## Related files

- `Generic-services-and-tags.md` – full lookup tables, keyword fallback and examples
- `RCOC-services-and-tags.md` – the matching reference for regional center services (columns I to L)
- `SKILL.md` – the full quarterly report workflow, including barriers and follow-ups

## Source of tags

*Standardized Community Navigator Measures Tag List* (Measures 19 and 31), FY24-25 SAE Community Connector Projects.

## Privacy

These files contain only rules and mappings. They hold no client names, UCI numbers or case details. Keep it that way; never add exports from the sheet or the Drive folders.
