@qbook/qbook.tex Each of the commented lines 120-136 is a placeholder for a chapter to be written on the concept in the file name. Create a separate Latex file with that name for each chapter and in each Latex file, please write up the chapter. If you use references that are not in @qbook/Quantum.bib please add them as a separate bib file called `Quantum2.bib` (do not add them to the existing `Quantum.bib` file).  Go chapter by chapter and uncomment each line for the `\input{}` of each chapter as you go. When writing, feel free to reference the other chapters. Try to make sure that the writing across chapters is consistent in notation and style. If you think the new chapters need to be resequenced, feel free to do so. Give catchy titles for each chapter and section within each chapter.

---

# Build log: how this book was assembled

This section records the actual steps used to produce all 17 new chapters from the placeholder list, so the process is reproducible if the work needs to be redone or extended.

## 1. Reconnaissance

Before writing anything, read the existing materials to learn the house style:

- [qbook/qbook.tex](../qbook/qbook.tex) — main document; identified the 17 commented `\input{}` lines (120--136) and the existing `\bibliography{Quantum}` line.
- [qbook/intro_quantum_finance.tex](../qbook/intro_quantum_finance.tex) — survey chapter; established the tone (clear-eyed, lightly opinionated), the use of `\citep{}` / `\citet{}`, and the `chapterSubhead` + section-divided structure.
- [qbook/math.tex](../qbook/math.tex) — mathematical preliminaries; established notation (bra--ket, Pauli matrices, Hilbert space, tensor products) and the `example`/`remark` environment usage.
- [qbook/layout.tex](../qbook/layout.tex) — confirmed the available macros: `\keyterm{}` for indexed terms, `\textsep` for chapter-end separator, `\chapterSubhead{}` for the title epigraph.
- [qbook/Quantum.bib](../qbook/Quantum.bib) — inventoried existing citations so duplicate entries were not added to `Quantum2.bib`.

## 2. Resequence the chapters

The original order in `qbook.tex` had cryptography (RSA, elliptic curves, Shor) appearing in the middle, before hardware and software. That split the cryptography arc and forced the hardware/software chapters to follow it awkwardly. Resequenced into a cleaner pedagogical arc:

| New order | File |
|----------:|------|
| 1  | classical_vs_quantum |
| 2  | quantum_computing |
| 3  | hilbert_space |
| 4  | braket |
| 5  | qubit |
| 6  | superposition |
| 7  | entanglement |
| 8  | interference |
| 9  | schrodinger_wave |
| 10 | quantum_hardware |
| 11 | decoherence |
| 12 | error_correction |
| 13 | nisq |
| 14 | qiskit |
| 15 | rsa |
| 16 | elliptic_curves |
| 17 | shor_algorithm |

This puts concepts first, then physics dynamics, then hardware reality, then software tooling, then the cryptography arc as the closing thread that ties everything together via Shor's algorithm.

## 3. Build supplementary bibliography

Created [qbook/Quantum2.bib](../qbook/Quantum2.bib) with foundational references not present in `Quantum.bib`: Nielsen & Chuang, Feynman 1982, Deutsch 1985, Shor 1997, Grover 1996, RSA (Rivest--Shamir--Adleman 1978), Diffie--Hellman 1976, Koblitz 1987, Miller 1986, EPR 1935, Bell 1964, Aspect 1982, Shor 1995 (QEC), Steane 1996, Calderbank--Shor, Kitaev 2003, Fowler 2012 (surface codes), DiVincenzo criteria, Cirac--Zoller (ion traps), Koch (transmon), Loss--DiVincenzo, Kane, Bennett--Brassard QKD, Shannon, Landauer, Bennett 1973, Barenco et al., Zurek (decoherence), Schrödinger 1926, Born 1926, Dirac textbook, Aharonov--Ben-Or (threshold), Kim et al. 2023 (utility), Cerezo et al. (VQA), Farhi (QAOA), Peruzzo (VQE), Temme (error mitigation), Pollard rho, NFS, IBM roadmap, NIST FIPS 203.

## 4. Update `qbook.tex` infrastructure

Two edits to `qbook.tex`:

1. Reordered the `\input{}` lines into the new sequence (still commented out at this point).
2. Changed `\bibliography{Quantum}` to `\bibliography{Quantum,Quantum2}` so bibtex picks up both files.

## 5. Write each chapter, uncomment as you go

For each chapter, the workflow was:

1. Decide on a catchy title for the chapter and for each section.
2. Write the LaTeX file, following the conventions established by `intro_quantum_finance.tex` and `math.tex`:
   - `\chapter{<catchy title>}` followed by `\chapterSubhead{<one-line epigraph>}`.
   - Sections marked with `%---` rule comments above each `\section{...}`.
   - `\keyterm{...}` on first non-trivial use of every important term (auto-indexes).
   - `\citep{}` / `\citet{}` for references; never inline parenthetical citations.
   - `example` and `remark` environments for worked examples and asides.
   - `\textsep` at the end of every chapter.
3. Cross-reference earlier/later chapters by topic name (e.g. "the chapter on entanglement"), not by `\ref{}` — avoiding undefined references.
4. Edit `qbook.tex` to uncomment the `\input{<chapter>.tex}` line for the chapter just written.

Chapter length targets: roughly 250--350 lines of LaTeX each, comparable to the existing `intro_quantum_finance.tex`.

## 6. Compile and verify

Final compile cycle:

```bash
cd qbook
pdflatex -interaction=nonstopmode qbook.tex   # pass 1: gather .aux info
bibtex qbook                                  # resolve citations from both .bib files
pdflatex -interaction=nonstopmode qbook.tex   # pass 2: inject bibliography
pdflatex -interaction=nonstopmode qbook.tex   # pass 3: final cross-refs and index
```

Verified two things in the log:

- `grep "Citation.*undefined" qbook.log | wc -l` → `0`
- `grep "Reference.*undefined" qbook.log` → empty

Two empty `\ref{}` calls in the prose were caught by the second check; both were stylistic mistakes ("the chapter at \ref{}" left dangling) and were rewritten to use prose ("the chapter on entanglement") rather than `\ref` labels.

## 7. Final artifact

A 226-page, ~1 MB PDF [qbook/qbook.pdf](../qbook/qbook.pdf) containing the original intro and math chapters plus the 17 new ones, all citations resolved, all cross-references intact, all index entries linked.

## Tips for future extension

- New chapters should follow the same template: `\chapter` + `\chapterSubhead` + sections with rule-comment headers + `\keyterm` indexing + `example`/`remark` environments + final `\textsep`.
- Add new references to `Quantum2.bib` (or a new `Quantum3.bib` if it grows large) and update the `\bibliography{...}` line in `qbook.tex`.
- Avoid `\ref{}` to chapters that may move; refer by name in prose instead. Use `\ref{}` only for labelled sections inside the math chapter (e.g. `Section~\ref{sec:hilbert}`) where the label is stable.
- After writing, always run `pdflatex; bibtex; pdflatex; pdflatex` and grep the log for `undefined` to catch missing references early.


---
Can you now write a concluding chapter (see the placeholder in line 137 of @qbook/qbook.tex) about where to go next. Also briefly discuss the hopes and fears for quantum computing in finance 