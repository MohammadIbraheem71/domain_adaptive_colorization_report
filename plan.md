# Plan — Chapter page breaks and the last visible Person A/B reference

Status: **implemented**. Build and PDF checks pass (see section 5).

Source: `f26_066_report/main.tex`. Line numbers refer to the current build.

Scope: restore the template's chapter pagination and remove the one visible
anonymous team-member reference. No result claims, numbers, test counts, tables
or figures change.

## 1. Verified state

- `main.tex:115` called `\continuouschapters` immediately after `\mainmatter`.
  Defined in `FastFyp.cls:83-92`, it makes `\chapter` a `titlesec` "straight"
  heading with no page break. `sample.tex` never calls it. As a result Chapter 7
  began mid-page and the Chapter 8 running head appeared over Chapter 7 text.
- `main.tex:2724` contained the only rendered "Person A"/"Person B" phrase, in
  the Integration row of the test-case summary table (PDF page 107). Other
  hits are a LaTeX comment (`main.tex:3784`), unused `person-c` labels
  (`main.tex:2571,2665,2676`), and A-/B-/C- test IDs whose full template cases
  already name the real authors (`main.tex:2754-2948`).

## 2. Chapter page breaks

Delete the `\continuouschapters` line, so `\mainmatter` is followed directly by
`\chapter{Introduction}`. This matches `sample.tex` exactly:

- The class already defines `\@makechapterhead` as a single-line "Chapter N
  Title" in `\Large\bfseries`, so the heading style is effectively unchanged.
- `FastFyp.cls` loads `book` with `oneside`, which defaults to `openany`, so
  chapters start on the next page without inserted blank verso pages.
- No edit to the university class file is needed.

## 3. Remove the visible Person A/B wording

Rewrite the Integration row's "What is checked" cell in the same words and
length, keeping the pipeline chain and every check:

> Integration & End-to-end flow with synthetic luminance: routing -> extraction
> -> mock experts -> blending -> reconstruction; verifies domains, fallback,
> patch/output shapes, expert color at region center, valid 8-bit RGB & 1

The non-rendered labels, comment and test-case IDs stay as they are.

## 4. Verification

- `pdflatex -interaction=nonstopmode main.tex` twice; exit 0, valid `%%EOF`.
- No undefined references or citations, no multiply-defined labels.
- Each of the 10 chapters, the bibliography and the appendix is the first body
  content on its own page; the Chapter 8 header no longer sits over 7.4 text.
- TOC chapter start pages regenerate; overfull count stays unchanged and no
  severe underfull box is introduced.
- `pdftotext main.pdf` contains no "Person A" or "Person B".

## 5. Result

- `\continuouschapters` removed; chapters now start on fresh pages like
  `sample.tex`. Page count 142 -> 147, TOC starts: Ch1 p1, Ch2 p4, Ch3 p8,
  Ch4 p23, Ch5 p37, Ch6 p55, Ch7 p74, Ch8 p103, Ch9 p104, Ch10 p124.
- Integration row now reads "End-to-end flow with synthetic luminance: ...".
- Build: `pdflatex` x2, exit 0, 147 pages, valid `%%EOF`, no undefined
  references or citations, no multiply-defined labels. Overfull boxes 23 and
  0 underfull vboxes, both unchanged. Underfull hboxes 162 -> 163: the one new
  entry is badness 2253 in the rewritten table row, no badness-10000 entries
  added.
