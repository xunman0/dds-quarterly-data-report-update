# RCOC Services Reference

A reference for filling the **Regional Center (RCOC) Services** columns of the quarterly DDS Service Access and Equity (SAE) DATA report. It maps each regional center funded service a client receives to its `RCSERV_*` tag.

The main file, [`RCOC-services-and-tags.md`](RCOC-services-and-tags.md), is written for AI agents. This README explains what it contains and how to use it.

## What it covers

| Sheet column | Header | What gets filled |
|---|---|---|
| I | Number of RCOC Services Before Program | Auto-count formula (never written) |
| J | RCOC Services | Service names from the dropdown |
| K | Services TAG | One or more `RCSERV_*` tags |
| L | Other Service(s) notes | Names of services tagged `RCSERV_OTHER` |

## Quick rules

- **Source:** the client's most recent IPP or CDER, section "Regional center funded services". No IFSP plans. No planned, pending or declined services.
- **Default for every client:** "Service Coordination / Case Management" goes in column J, `RCSERV_OTHER` in column K, and the service name in column L. Add it even when the IPP/CDER does not list it.
- **Specific tag first:** respite, social, behavior, therapy and day program services have their own tags. Do **not** repeat them in column L.
- **`RCSERV_OTHER` + notes:** every service that maps to `RCSERV_OTHER` is also named in column L.
- **Tag order:** put `RCSERV_OTHER` first, no duplicates.
- **Multi-values:** comma + space separated. No tabs, newlines or quotes.

## Tag cheat sheet

| Tag | Services |
|---|---|
| `RCSERV_RESPITE` | In-Home Respite Services Agency (including #2 and the mileage reimbursement version) |
| `RCSERV_SOCIAL` | Participant-Directed Social Recreation Services, Social Recreational Services, Social Recreation Program, Purchase Reimbursement (Social Recreational Services), Community Activities Support Services |
| `RCSERV_BEHAVIOR` | Behavior Analyst |
| `RCSERV_THERAPY` | Infant Development Program(therapy), Infant Development Specialist, Speech Pathology |
| `RCSERV_DAYPROGRAM` | Adult Development Center |
| `RCSERV_OTHER` | Service Coordination / Case Management, Personal Assistance, Transition From Early Start Services, Individual or Family Training Services, Financial Management Service (Co-Employer and Fiscal / Employer Agent), Transportation services, Home Health Agency (LVN and IARE), Supported Employment Program - Group Services, Independent Living Program |

Other valid tags with no service currently mapped: `RCSERV_DAYCARE`, `RCSERV_JOB`, `RCSERV_LIVING`.

## Example

A client with in-home respite and individual or family training:

| Column | Value |
|---|---|
| J | Service Coordination / Case Management, In-Home Respite Services Agency, Individual or Family Training Services |
| K | RCSERV_OTHER, RCSERV_RESPITE |
| L | Service Coordination / Case Management, Individual or Family Training Services |

## How it is used

1. Read the client's IPP or CDER ("Regional center funded services").
2. Add Service Coordination / Case Management, then map each other service using the lookup table in `RCOC-services-and-tags.md`.
3. Write the values to the sheet by pasting tab-separated rows (dropdown cells reject typed text).
4. Verify by reading the sheet back as CSV and comparing it with what was intended.
5. Report any service that was not in the lookup table.

## Judgment calls

- **Supported Employment Program - Group Services** and **Independent Living Program** are tagged `RCSERV_OTHER`, although `RCSERV_JOB` and `RCSERV_LIVING` are plausible matches. Follow the lookup table unless told otherwise.
- **Personal Assistance** and the **Home Health Agency** services have no closer tag, so they stay `RCSERV_OTHER`.

## Related files

- `RCOC-services-and-tags.md` – full lookup table, keyword fallback and examples
- `Generic-services-and-tags.md` and `README-generic-services.md` – the matching reference for generic services (columns N to R)
- `SKILL.md` – the full quarterly report workflow, including barriers and follow-ups

## Source of tags

*Standardized Community Navigator Measures Tag List* (Measures 18 and 32), FY24-25 SAE Community Connector Projects.

## Privacy

These files contain only rules and mappings. They hold no client names, UCI numbers or case details. Keep it that way; never add exports from the sheet or the Drive folders.
