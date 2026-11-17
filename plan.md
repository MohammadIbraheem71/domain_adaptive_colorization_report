# Plan — Rework 7.3 Test Metrics

Status: **implemented** (formatting/structure only). See section 5 for the result.

Goal: make Section 7.3 of `f26_066_report/main.tex` follow the structure of
Section 7.3 in `sample.tex` (university ground truth), using the verified
E1/E2/E4 result files where they support the text.

## 0. Decisions taken

1. Test-case effectiveness is reported as **100%** (failed tests used as the
   defect proxy: 0 failed / 178 executed).
2. The traceability matrix covers **FR1–FR12 only** (no objective mapping).
3. **Do not change any result claims in this pass.** Correct the E1 rounding
   and the E4/Chapter 10 wording only after the formatting work is finished
   (see Deferred).
4. Result CSVs/JSON stay **local**; `results/` remains ignored in the code repo.
5. `sample.tex` structure is the target: intro → `Metric | Purpose` table
   (six rows) → one `\subsection` per additional metric table.

## 1. Verified result files (checked 2026-11-17)

- E1 `summary.json` / `summary.csv`: mIoU 0.6241, domain mIoU 0.6300,
  coverage pooled 0.4266, fallback 0.5734, 500 test images,
  `selected_params.json` tuning `n_images=200`. Matches the report.
- E1 `confusion_groups.csv`: animal→person fraction is **0.081**, report says
  0.082. Single rounding mismatch; deferred.
- E2 `routing_summary.json`: 2,773 regions, 47 discarded, 727 excluded,
  2,046 scored, accuracy 0.9218. Matches the report.
- E4 `summary_table.csv`: 13 conditions × 100 images, all rounded means match
  the report tables.
- E4 `validation.txt` / `results.csv`: 7,800 rows, 0 duplicates,
  0 missing decompositions.
- E3 per-set raw results are still missing; only the E3-T aggregate row inside
  `e4_results` (`e3_ddcolor_t_whole`) exists. No action in this pass.

## 2. Rewrite of 7.3 (plan as written before implementation)

Original 7.3 (`main.tex` ~line 2994) had an intro, "Test levels", the 178-test
module table and an "Execution and status" paragraph. It had no metric table
and no subsections, so it did not match `sample.tex`. The items below are the
plan; section 5 records where the implementation differed.

### 7.3 Test Metrics

Short intro: this section reports **test-process** metrics, separate from the
image-quality metrics of Chapter 9.

Table `tab:test-metrics`, two columns (`Metric` | `Purpose`), six rows:

| Metric | Purpose / value |
| --- | --- |
| Number of Test Cases | 178 automated test functions in 12 modules under `tests/`. |
| Number of Test Cases Passed | 178; all executed tests passed in the recorded environment. |
| Number of Test Cases Failed | 0. |
| Test Case Defect Density | (0 failed × 100) / 178 executed = 0%. |
| Test Case Effectiveness | 100%: 178 of 178 cases passed, so no failed test exposed an undetected defect. |
| Traceability Matrix | Table `tab:req-traceability` maps FR1–FR12 to modules and tests. |

### 7.3.1 Test Suite Composition

- Move the existing "Test levels" paragraph here unchanged.
- Keep `tab:test-modules` (178 tests, 12 modules) unchanged; it is the second
  metric table required by the sample ("add here for test metrics 2,3,4").
- Keep the environment sentence (Linux, Python 3.13, pytest, no GPU).
- Drop the "earlier count of 176" sentence or fold it into one clause; the
  table is now the authoritative count.

### 7.3.2 Traceability Matrix

New table `tab:req-traceability` (longtable, `p{}` columns) — renamed during
implementation because `tab:traceability` is already used by the
objective-mapping table in Chapter 4:

| Req. | Module | Automated evidence |
| --- | --- | --- |
| FR1 grayscale input | `main.py`, pipeline | `test_integration.py` |
| FR2 region decomposition | `colorize/decomposition.py` | `test_decomposition.py` (14), TC-A-01/02/03 |
| FR3 contextual extraction (~20% margin) | `colorize/extraction.py` | `test_colorization.py` (31), TC-B-01 |
| FR4 routing | `colorize/routing.py` | `test_routing.py` (14) |
| FR5 expert colorization | `colorize/experts.py` | `test_colorization.py` (31) |
| FR6 multi-expert image | `colorize/pipeline.py` | `test_integration.py` (1) |
| FR7 soft mask blending | `colorize/blending.py` | `test_colorization.py`, TC-B-02 |
| FR8 position-preserving reconstruction | `colorize/pipeline.py` | `test_integration.py` |
| FR9 session display | `gui/` | **manual only — HTML prototype, no automated test** |
| FR10 stage evaluation | `colorize/metrics.py`, `colorize/seg_eval.py` | `test_metrics.py` (39), `test_seg_eval.py` (32) |
| FR11 add expert without retraining | `configs/class_groups.json` | **partial — config-driven, no dedicated test** |
| FR12 fallback expert | `colorize/routing.py` | `test_routing.py`, TC-A-02 |

Closing paragraph:

- 178 of 178 tests pass; defect density 0%; effectiveness 100%.
- 10 of 12 requirements are covered by automated tests; FR9 is manual and
  FR11 is partial.
- Tests use synthetic arrays, rectangular masks and model stand-ins, so no
  GPU, checkpoint or COCO download is required.

## 3. Deferred (explicitly NOT in this pass)

- E1 animal→person `0.082` / `8.2%` → `0.081` / `8.1%` (lines ~3301, ~3317).
- "matches or exceeds … on all reference-based metrics" at lines ~3618 and
  ~3719: false for SSIM (0.9057 vs 0.9107) and LPIPS (0.1506 vs 0.1501).
- "margin 0.2 is optimal": 0.1 is better on Boundary ΔE, Lab ΔE, SSIM, LPIPS;
  reword to a flat 0.1–0.2 optimum chosen on PSNR.
- "σ₀=10 is near the plateau": σ₀=20 is best on five of six metrics.
- `results/e4_results/results/e4/e4_notes.md` contradicts the run (claims SAM
  masks essential while `shape_box` wins every metric); rewrite or delete.
- E3 per-set raw results are unavailable; keep the existing E3 table as is.

## 4. Verification for this pass

- `pdflatex -interaction=nonstopmode main.tex` twice succeeds (exit 0, valid
  `%%EOF`); no undefined references or duplicate labels. `latexmk` is not
  usable in this repo: it trips over the pre-existing bibliography/aux state,
  so the manual two-pass build is the verification method.
- TOC shows 7.3, 7.3.1 and 7.3.2 in that order; LOT shows Tables 7.18–7.20.
- 7.3 table has exactly the six sample metrics and the 100% effectiveness row.
- Module counts still total 178; traceability covers FR1–FR12 with no gaps
  hidden (FR9 manual, FR11 partial).
- No overfull boxes in 7.3; no changes outside 7.3 in the report source.

## 5. Implementation result

- 7.3 now follows `sample.tex`: intro → six-row `Metric | Purpose` table
  (Table 7.18) → `7.3.1 Test Suite Composition` (Table 7.19, the 178-test
  module table) → `7.3.2 Traceability Matrix` (Table 7.20, FR1–FR12).
- Metric values: 178 cases, 178 passed, 0 failed, defect density 0%,
  effectiveness 100%.
- Traceability marks FR9 as manual only and FR11 as partial; the closing
  paragraph states that 10 of 12 requirements are automated.
- Table 7.18 uses `[H]` so it stays between the intro and 7.3.1. The
  traceability longtable starts on a fresh page and fits on that page with
  the 7.3.2 closing paragraph.
- Underscores in `\texttt{...}` do not render with `mathptmx` (the glyph is
  missing, so `test_metrics.py` prints as `test metrics.py`). All paths in 7.3
  now use `\path{...}`, which renders the underscore and breaks at `/`.
- Final build: `pdflatex` ×2 succeeds, 141 pages, no undefined references or
  citations, no multiply-defined labels, no overfull boxes in 7.3.
- Note for future builds: VS Code LaTeX Workshop auto-builds on save and can
  race a manual `pdflatex`, truncating `main.pdf`/`main.aux`. Wait for it to
  finish before rebuilding.

### Deltas from the plan in section 2

- Traceability label is `tab:req-traceability` (not `tab:traceability`, which
  is taken by the Chapter 4 objective-mapping table).
- The traceability table has four columns (Req., Requirement, Implemented in,
  Test evidence), not the three-column sketch in the plan.
- The effectiveness row explains the 100% via the 178/178 pass count instead
  of a divide-by-zero-style formula.
- Table 7.18 is fixed in place with `[H]`; the traceability longtable starts
  with `\clearpage` and fits on one page with its closing paragraph.
- Paths in 7.3 use `\path{...}` (plus `\UrlBreaks` and `\mbox` for TC IDs)
  because `mathptmx` drops the underscore glyph in `\texttt{...\_...}`.
- Verification used two manual `pdflatex` passes rather than `latexmk`.

### Still open (not in this pass)

- The same `\texttt{...\_...}` underscore problem exists in about 50 other
  places outside 7.3 (e.g. `data_lists/`, `outputs/e3_ids.json`, ablation
  condition names). Replace with `\path{...}` in a later formatting pass.
- Page 98 is headed "CHAPTER 8. USER MANUAL" while it still contains the end
  of the 7.4 conclusion, because the chapter starts mid-page. Pre-existing;
  fix with a page break before `\chapter{User Manual}` if desired.

## Appendix — completed in earlier passes

- Added 7.3, 7.4, 9.1–9.7, 10.6 and 10.7; fixed the dangling `Section~7.14`
  reference; test count normalised to 178.
- Added the Chapter 9 experimental setup and conclusion, and the Chapter 10
  phased FYP-2 milestone table.
- Fixed the duplicate `tab:literature-summary` label.
- Added `sample.tex` as the formatting ground truth (`7b38db6`).
