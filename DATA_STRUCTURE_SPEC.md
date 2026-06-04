# DATA_STRUCTURE_SPEC — Comparison_AI&JORT_Dec2_2025_v3.xlsx

> **Purpose.** Exact, cell-level contract between the raw workbook and the analysis pipeline. Nothing is written to code until this spec is signed off. Everything in §1–§6 is stated in terms of real cell coordinates verified against the current v3 file (size 28,506 bytes, last modified 2026-04-12 15:36).

File: `/mnt/2. Experiment/Comparison_AI&JORT_Dec2_2025_v3.xlsx`
Sheets (4): `Unlimited Themes`, `3 Themes`, `Scholar Lens`, `Drift Experiment`
Previous sheet `SL no Theme Limit` has been dropped from the project and is not parsed.

---

## 1. Global parsing rules (applied to every sheet)

1. Read with `openpyxl.load_workbook(..., data_only=True)` (not pandas), so merged-cell geometry is preserved.
2. For every `ws.merged_cells.ranges`, forward-fill the top-left value to every cell inside the range **before** any column-wise logic runs. Every merge is listed explicitly in §2–§5 below.
3. Row 1 across every sheet carries the JORT reference themes (banner). Header row is row 2 on `3 Themes`, `Scholar Lens`, `Drift Experiment`; header row is row 3 on `Unlimited Themes` (row 2 on this sheet is the JORT banner). Data begins one row below the header row.
4. Each raw AI output cell is normalized by:
   - stripping residual time prefixes matched by regex `^\s*(?:\(?[0-9]+\s*(?:m|min|mins|minutes)\s*)?(?:[0-9]+\s*s(?:ec|econds)?)?\)?[\s,:\.–-]*` applied only to the first ~20 characters,
   - collapsing `\r\n`, `\n`, `⏎` into `\n`,
   - stripping leading numeric bullets `^\s*\d+[.,)]\s*` on every line,
   - lowercasing a `_norm` copy for similarity calculations while retaining the original `_raw` copy for manuscript quotation.
5. Every intermediate artifact is written to `/mnt/REI Manuscript/8. Results/` and is named `records_<sheet>.csv` for the long-form records and `<metric>_<sheet>.csv` for derived metrics. Every such file begins with a commented provenance row: `# generated YYYY-MM-DD HH:MM by notebook vN cell MD5=<hash>`.
6. No pandas operation that silently drops NaN is permitted; all filtering must be expressed as an explicit boolean mask and accompanied by an `_audit_log.txt` line stating what was dropped and why.

---

## 2. Sheet `Unlimited Themes` (17 rows × 5 cols, 13 merges)

**Header row (R3):** `A=No.  B=AI Model  C=Prompt focus  D=Prompt  E=AI output (Sep 17, 11:00am)`

_Time consumption column and Similarity column (with author's `√` markers) were removed by the user during data cleanup. The `author_flagged` feature is therefore dropped from the pipeline._

**Merged cells and their semantics**

| Range | Meaning after forward-fill |
|---|---|
| A1:D1 | Sheet banner "Thematic analysis" (ignored) |
| A2:G2 | JORT reference themes (ignored by parser; shown in manuscript exhibit) |
| C4:C5 | `Prompt focus = Basic` for rows 4–5 |
| C6:C9 | `Prompt focus = Role` for rows 6–9 |
| C10:C13 | `Prompt focus = Domain` for rows 10–13 |
| C14:C17 | `Prompt focus = Method` for rows 14–17 |
| D4:D5 | Shared prompt `P1` (Basic) for rows 4–5 |
| D6:D7 | Shared prompt `P2` (Role variant A) for rows 6–7 |
| D8:D9 | Shared prompt `P3` (Role variant B) for rows 8–9 |
| D10:D11 | Shared prompt `P4` (Domain variant A) for rows 10–11 |
| D12:D13 | Shared prompt `P5` (Domain variant B) for rows 12–13 |
| D14:D15 | Shared prompt `P6` (Method variant A) for rows 14–15 |
| D16:D17 | Shared prompt `P7` (Method variant B) for rows 16–17 |

**Records produced.** 14 rows → 14 records. One record per (row 4…17). Each record carries
`{sheet:"unlimited", no, model, focus, prompt_id ∈ {P1…P7}, prompt_text, output_raw, output_norm}`.

**Model coding.** `B` is one of `{ChatGPT 5 (Auto), Claude (Sonnet 4)}`. These two models alternate inside every prompt pair.

**Pairing rule for cross-model agreement.** Inside each prompt group P1…P7 there is exactly one ChatGPT 5 output and one Claude Sonnet 4 output, so strict identical-prompt pairing yields **7 pairs** (not 45). The §4.1 table must report 7.

**Known residuals.** No time-prefix residuals remaining on this sheet after user cleanup.

---

## 3. Sheet `3 Themes` (6 rows × 5 cols, 3 merges)

**Header row (R2):** `A=No.  B=AI Model  C=Prompt focus  D=Prompt  E=AI output (Sep 24, 9:00am)`

_Time consumption column has been removed. Empty padding rows R7–R33 have been deleted._

**Merges**

| Range | Meaning |
|---|---|
| A1:E1 | JORT banner (ignored) |
| C3:C6 | `Prompt focus = "3 Themes"` applies to every data row |
| D3:D6 | **One shared prompt** applies to every data row |

**Data rows.** R3–R6 carry data (no padding). The pipeline asserts `ws.max_row == 6` and `len(non_empty_rows) == 4`.

**Models (B column).**
R3: `ChatGPT 5 (Auto)`
R4: `ChatGPT 5 (Thinking + Study)`
R5: `Claude (Sonnet 4)`
R6: `Claude (Sonnet 4) Reaserch + Extended Thinking`  (note user's own spelling `Reaserch`; preserved verbatim in records, displayed as `Research` in manuscript)

**Pairing rule for cross-model agreement.** All 4 outputs answer the same prompt → `C(4,2) = 6 pairs`. §4.2 must report 6.

**Known residuals.** None.

---

## 4. Sheet `Scholar Lens` (6 rows × 15 cols, 8 merges)

_All six Time consumption columns have been removed. Each scholar block is now 2 columns wide (Prompt + Output)._

**Header row (R2) describes six scholar blocks of width 2 columns each.**

Column layout:
```
A=No.   B=AI Model   C=Prompt focus
[block Hunt    ] D=Prompt  E=AI output (Sep 30, 8:54pm)
[block Pan     ] F=Prompt  G=AI output (Sep 30, 9:40pm)
[block Mowen   ] H=Prompt  I=AI output (Sep 30, 9:40pm)
[block Fletcher] J=Prompt  K=AI output (Oct 1, 1:05pm)
[block Escobar ] L=Prompt  M=AI output (Oct 7, 11:15am)
[block Crompton] N=Prompt  O=AI output (Oct 7, 10:09pm)
```

Scholar→block mapping is derived from the text of the merged prompt cell (the prompt names the scholar explicitly) and is cross-checked in the notebook by an `assert` that the scholar's surname appears in the prompt. Order as verified against v3 and confirmed with the project description: Hunt → Pan → Mowen → Fletcher → Escobar → Crompton.

**Merges**

| Range | Meaning |
|---|---|
| A1:E1 | JORT banner |
| C3:C6, D3:D6 | `Prompt focus = 3 Themes` and single shared Hunt prompt for R3–R6 |
| F3:F6 | Single shared Pan prompt |
| H3:H6 | Single shared Mowen prompt |
| J3:J6 | Single shared Fletcher prompt |
| L3:L6 | Single shared Escobar prompt |
| N3:N6 | Single shared Crompton prompt |

Output columns E, G, I, K, M, O are **not** merged — one output per (model × scholar).

**Records produced.** 4 models × 6 scholars = **24 records**. Each record carries
`{sheet:"scholar", model, scholar ∈ {Hunt, Pan, Mowen, Fletcher, Escobar, Crompton}, prompt_text, output_raw, output_norm}`.

**Pairing rules.**
- Within-scholar cross-model pairs: `C(4,2) × 6 scholars = 36 pairs` (answers §5 reproducibility per scholar).
- Within-model cross-scholar pairs (distinctiveness): `C(6,2) × 4 models = 60 pairs`.
- Scholar-to-JORT similarity: 24 records × 3 JORT themes = 24 summary rows (max-pairwise and mean-pairwise).

---

## 5. Sheet `Drift Experiment` (22 rows × 17 cols, 9 merges)

_Empty padding rows R23–R45 have been deleted. Drift never had a dedicated Time column; time annotations remain embedded inside a few output cells and are handled by the §1.4 regex scrubber._

**Header row (R2):**
```
A=No.  B=AI Model  C=Prompt  D=AI Output (Round 0, baseline)
E=Follow-up Tone  F=Follow-up Prompt (Repeated)
G=Output R1  H=R2  I=R3  J=R4  K=R5  L=R6  M=R7  N=R8  O=R9  P=R10
Q=(spill — normally empty)
```

**Four model blocks, 5 follow-up tones each.**

| Rows | Model | Merge on B and C |
|---|---|---|
| R3–R7 | ChatGPT 5.1 (Auto) | B3:B7, C3:C7 |
| R8–R12 | Claude Sonnet 4.5 | B8:B12, C8:C12 |
| R13–R17 | ChatGPT 5.1 (Thinking + Study) | B13:B17, C13:C17 |
| R18–R22 | Claude Sonnet 4.5 (Reaserch + Extended Thinking) | B18:B22, C18:C22 |

Tones appear in fixed order inside every block: `Supportive, Mild Rejection, Strong Rejection, With Lens (Carter), With Lens (Bing)`.

Pipeline asserts `ws.max_row == 22` and `len(non_empty_rows) == 20` (no padding after cleanup).

**Records produced.** 20 groups × 11 rounds (Round 0 in D plus Rounds 1–10 in G–P) = **220 output records** in the dense case. Each record carries
`{sheet:"drift", model, tone, round ∈ 0…10, prompt_baseline_text, followup_text, output_raw, output_norm, non_compliant_flag, non_compliance_reason}`.

**Non-compliance taxonomy (already observed in v3).**

| Cell | Reason code | Treatment |
|---|---|---|
| R5 / O5 (CGPT 5.1 Auto, Strong Rejection, R9) | `time_annotation` — content prefixed with "(Longer time: 1m15s)" then valid 3 themes | parse themes; record `time_note="1m15s"` |
| R16 / N16 (CGPT 5.1 Thinking+Study, With Lens Carter, R8) | `principled_refusal` — 1m50s plus meta-commentary "Gotcha — my lists clearly aren't matching..." | flag as non-compliant; no themes extracted |
| R16 / O16 and P16 | `rollover_NA` after refusal | non-compliant |
| R18 / P18 (Claude 4.5 R+ET, Supportive, R10) | `meta_commentary` — "I appreciate your engagement with this iterative process..." | non-compliant |
| R18 / Q18 | `spillover` — "What You Should Do Now..." | dropped from analysis, logged |
| R20 / H20 (Claude 4.5 R+ET, Strong Rejection, R2) | `apology` — 27s then "I apologize..." | non-compliant; themes set to NaN |
| R20 / I20, J20 | `apology_chain` | non-compliant |
| R20 / K20–P20 | `rollover_NA` (cells literally "N/A") | non-compliant |
| R21 / H21 (Claude 4.5 R+ET, With Lens Carter, R2) | `report_form` — "Your research report on Dr. Carter A. Hunt is ready..." | non-compliant; classifier drops from similarity comparisons but keeps for drift trajectory plot |
| R22 / G22 (Claude 4.5 R+ET, With Lens Bing, R1) | `report_form` | non-compliant same treatment |

**Typos preserved in raw records but corrected silently in `_norm` copy.**

| Cell | Raw | Normalized |
|---|---|---|
| I16 | "2 Relational place-making" | "2. Relational place-making" |
| N15 | "1," and "2," bullets | "1." and "2." |
| P19 | "Adoptio" (truncated) | "Adoptio" (preserved — user to flag if this is a data-entry error) |

**Residual time prefixes still present.** N16 ("1m50s ⏎"), O5 ("(Longer time: 1m15s) ⏎"), H20 ("27s ⏎"). All three are scrubbed by the time-prefix regex (§1.4) before similarity computation, and the scrubbed seconds are logged to `records_drift.csv.time_note`.

---

## 6. Derived artifacts (exactly what the notebook will emit)

| Artifact | Rows | Description |
|---|---|---|
| `records_unlimited.csv` | 14 | one row per Unlimited Themes output |
| `records_three_themes.csv` | 4 | one row per 3 Themes output |
| `records_scholar.csv` | 24 | one row per Scholar Lens output (4 models × 6 scholars) |
| `records_drift.csv` | up to 220 | one row per (group × round); `non_compliant_flag` set where applicable |
| `jort_similarity.csv` | 14 + 4 + 24 + 80 (drift R0+R10) = 122 | Lexical-Jaccard, Synonym-Jaccard, TF-IDF-char-trigram cosine, each output vs each of the 3 JORT themes; max and mean across JORT |
| `cross_model_agreement.csv` | 7 (Unlimited) + 6 (3T) + 36 (Scholar within-scholar) = 49 | pairwise similarities inside identical-prompt groups |
| `scholar_distinctiveness.csv` | 60 | pairwise similarities between scholars within the same model |
| `drift_analysis.csv` | 20 × (10 within-group R0→Rn + local R(n−1)→Rn + global JORT similarity) | per-round drift trajectory |
| `non_compliant_outputs.csv` | ~10 | taxonomy table as in §5 |
| `_audit_log.txt` | ~300 lines | every filter / assertion / drop with row identity |

Three similarity metrics run per comparison:
- **Lexical Jaccard** on the set of lemmatized content-word tokens (stopwords removed, spaCy `en_core_web_sm`).
- **Synonym-Aware Jaccard** that maps terms through `synonym_groups.csv` (curated lexicon of 33 groups / 470 terms — preserved unchanged from the previous iteration, subject to user review).
- **TF-IDF cosine** over character trigrams `n=3` (robust to pluralization and compound splitting).

Every metric is computed by two independent implementations inside the notebook (hand-rolled Python using `collections.Counter` plus `sklearn.feature_extraction.text`) and the two outputs are required to agree to 1e-9; any disagreement halts with an audit error.

---

## 7. Version confound (methodological note, not a parsing rule)

The static sheets (`Unlimited`, `3 Themes`, `Scholar Lens`) use **ChatGPT 5** and **Claude Sonnet 4**. The `Drift Experiment` sheet uses **ChatGPT 5.1** and **Claude Sonnet 4.5**. This is a real version confound. The notebook will never silently collapse 5 with 5.1 nor 4 with 4.5. The manuscript reports them as `ChatGPT 5 family` and `Claude Sonnet 4 family` only when a claim genuinely applies to the family; otherwise the specific version is cited. A dedicated footnote in §3 Methods will make this explicit.

---

## 8. Decisions (finalized 2026-04-13)

1. **Unlimited Themes cross-model pairing:** identical-prompt only → **7 pairs**.
2. **Model-family handling:** never collapse 5/5.1 or 4/4.5; explicit versions throughout.
3. **`√` markers / author_flagged:** dropped (column deleted by user during cleanup).
4. **Notebook format:** `.ipynb`.
