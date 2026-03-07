# Retest 0306-3: CCP-0306-AUDIT-FIXES + CCP-0306-FULL-DELIVERY Validation

**Date:** 2026-03-06
**Code version:** main @ 9b9c9ca (audit-fixes merged)
**Purpose:** Retest 37 degraded addresses from Batch94 (32 LITE_PACKET + 5 FAILED) against final deployed code with both CCPs merged.

## CCPs Under Test

1. **CCP-0306-FULL-DELIVERY** -- QG recovery (graduated ratio tolerance, intentionalOverride) + assessment-only packet tier (zero comps + assessment data)
2. **CCP-0306-AUDIT-FIXES** -- 4 P0 formatting fixes, 2 P1 stuck-order fixes, 5 P2 alert/guard fixes

## Results

| Metric | Batch94 | Retest 0306-3 | Delta |
|--------|---------|---------------|-------|
| Full packets (APPEAL) | 0 | 6 | +6 |
| DO_NOT_APPEAL | 0 | 7 | +7 |
| Lite (QG fallback) | 0 | 10 | +10 |
| Lite (data gap) | 32 | 6 | -26 |
| Failed | 5 | 6 | +1 |
| Refund (agricultural) | 0 | 2 | +2 |
| **Total delivering** | **32** (lite only) | **29** (13 full/dna + 16 lite) | **+13 upgraded** |

**Pass rate:** 29/37 (78%), or 29/35 non-agricultural (83%)

## Status Breakdown

| Status | Count |
|--------|-------|
| completed | 22 |
| do_not_appeal | 7 |
| quality_gate_failed | 6 |
| refund_pending | 2 |

## Packet Type (passing only)

| Type | Count |
|------|-------|
| lite_qg_fallback | 10 |
| do_not_appeal | 7 |
| full | 6 |
| lite | 6 |

## Data Source (passing only)

| Source | Count |
|--------|-------|
| reapi | 13 |
| unknown (county scrape) | 10 |
| reapi+quantarium | 6 |

## Failures (8)

### Quality Gate Failed (6) -- same root cause
All 6 fail with: `NO_PLACEHOLDERS: Found placeholders: NaN`

| # | Address | State | Gate Reason |
|---|---------|-------|-------------|
| 6 | 9540 Cliffs Trl, Boulder Junction, WI | WI | provider_data_gap |
| 7 | 15830 22 Mile Rd, Big Rapids, MI | MI | provider_data_gap |
| 9 | 260 Breezy Point Ln, Malta, MT | MT | zero_comps |
| 13 | 2340 Sunset Blvd, Bar Nunn, WY | WY | zero_comps |
| 24 | 119 Mountain Dr, Andreas, PA | PA | zero_comps |
| 27 | 404 S 6th St, Onida, SD | SD | provider_data_gap |

Root cause: NaN propagating into lite packet HTML from missing provider data. Quality gate correctly rejects. Next fix target.

### Refund -- Agricultural (2) -- correct behavior
| # | Address | State |
|---|---------|-------|
| 36 | 4435 Georgetown Rd, Lexington, KY | KY |
| 37 | 2478 N Farm Rd 231, Strafford, MO | MO |

## Processing Time (passing)
- Average: 73s
- Min: 46s
- Max: 167s
- Total runtime: 889s (14.8 min)

## Verdict

13 of 37 degraded addresses upgraded from lite-only to full/dna packets. CCP-0306-FULL-DELIVERY is validated. 6 remaining QG failures share a single NaN root cause in the lite packet template path.
