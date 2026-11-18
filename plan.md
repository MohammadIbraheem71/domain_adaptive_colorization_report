# Plan — Chapter 7: keep only Section 7.1

Source: `f26_066_report/main.tex`. Line numbers are from the current 147-page
build at `e772c94`.

Scope: Chapter 7 only. Keep 7.1, disable 7.2 and 7.3, rewrite the Chapter 7
conclusion, fix cross-references, and keep the remaining prose coherent.
No result claims, numbers, tables or figures in other chapters change.

## What needs to be done

1. Keep Section 7.1 (`main.tex:2212–2677`) as it is, except for step 2.

2. At `main.tex:2580`, remove the trailing parenthetical
   `(Section~\ref{sec:test-metrics})` from the extensibility sentence; it is
   the only live reference into the sections being disabled.

3. Comment out the continuous block `main.tex:2679–3147`, i.e. Section 7.2
   (`Test case Design and description`, with 7.2.1–7.2.3.5) and Section 7.3
   (`Test Metrics`, with 7.3.1–7.3.2). Wrap it in `\iffalse` / `\fi` and put
   the markers on their own lines immediately before `\section{Conclusion}` at
   `main.tex:3149`. There are no nested conditionals in the block. The
   “Ten of the twelve requirements…” paragraph at `main.tex:3147` is the tail
   of 7.3 and is already inside the block.

4. At `main.tex:2208`, rename the chapter heading from
   `\chapter{Implementation and Test Cases}` to `\chapter{Implementation}`.
   Otherwise the chapter title and TOC still advertise the removed sections.

5. At `main.tex:2210`, shorten the Chapter 7 opening so it no longer says the
   chapter covers “the test cases”; keep the implementation and evaluation
   tooling description.

6. At `main.tex:2204`, change the Chapter 6 forward reference from “The next
   chapter describes the implementation and test cases;” to “The next chapter
   describes the implementation;”.

7. Replace the Chapter 7 conclusion (`main.tex:3149–3162`) with one paragraph
   about Section 7.1 only: the five-stage NumPy/PyTorch pipeline, configuration
   and shared data structures, CLI and HTML concept, E4 runner with
   prediction sharing and CSV resumability, data-driven extensibility,
   evaluation tooling (metrics, fixed COCO sets, CSV records, plotting), and
   status against the design. No test counts, pass rates, defect statements or
   requirement-traceability claims.

8. Leave untouched: Tables 7.2 and 7.3 (`tab:a_params`, `tab:a_label_map`),
   Equations (7.2) and (7.3), all of Section 7.1’s 13 subsections, and every
   other chapter. Also leave the coherent material that does not point into
   7.2/7.3: §6.6 *Policies and Tactics* (`main.tex:2163`, `2173`, `2181`) and
   `tab:traceability` (`2183`), the pytest row in the tools table (`2232`),
   the “Extensibility test” procedure (`2578`), and the evaluation-tooling
   paragraphs (`2589`, `2623–2671`).

9. Build with `pdflatex -interaction=nonstopmode main.tex` twice. Check that
   the chapter title in the TOC is “Implementation”, that there are no 7.2/7.3
   test sections and no Table 7.20 in the List of Tables, that Tables 7.2/7.3
   remain, that there are no undefined references or multiply-defined labels,
   that no “178-test suite” text remains in Chapter 7, and that overfull boxes
   do not increase.

10. Do not commit or push the report changes until asked.
