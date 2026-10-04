# Prob Library

### Author: Kyle Monette, [kylemonette.github.io](https://kylemonette.github.io)

#### Updated: October 4, 2026

---

`problib` is a LaTeX package for keeping a library of problems and building assignments from it using the `exam` class.
Each assignment records which problems it used, and a catalog document collects that history so you can see when and where every problem was last used.

Assignments compile with pdfLaTeX, XeLaTeX or LuaLaTeX; the catalog requires LuaLaTeX.

The full manual is [`problib.pdf`](problib.pdf). Working examples are in this repository:
[`library/`](library) holds sample problems and a catalog, and [`examples/`](examples) holds a
worksheet and an exam built from them.

## Installation

**From CTAN:** `problib` is now [available on CTAN](https://ctan.org/pkg/problib) and can be easily downloaded from there.

**From this repository:** the package is distributed as a `.dtx`/`.ins` pair,
the standard format for LaTeX packages. To build and install it by hand:

```sh
tex problib.ins        # generates problib.sty
pdflatex problib.dtx   # generates problib.pdf (run twice)
```

Then move `problib.sty` into a directory TeX searches (for example, your local texmf tree).



## Problem files

One problem per file. A problem is referenced by its path inside the library, without `.tex`;
Moving or renaming a file starts a new usage history for it.

```latex
% library/calculus/limits/squeeze-01.tex
\begin{problem}[
  title      = Chain rule with trig,
  topics     = {calculus, derivatives},
  tags       = {chain rule, trig},
  difficulty = 2,
  points     = 10,
  time       = 5,
  author     = John Smith,
  created    = 2025-08-14,
]
Differentiate each function.
\begin{parts}
  \part $f(x) = \sin(x^2)$
  \part $g(x) = \cos^3(2x)$
\end{parts}
\begin{solution}
  (a) $2x\cos(x^2)$ \qquad (b) $-6\cos^2(2x)\sin(2x)$
\end{solution}
\end{problem}
```

## Assignments

```latex
% examples/exam1.tex
\documentclass[addpoints]{exam}
\usepackage{amsmath}
\usepackage[library = ../library]{problib}

\problibassignment{
  name   = Exam 1,
  course = MATH 221,
  term   = Fall 2026,
  type   = exam,
  date   = 2026-10-12,
}
\problibsetup{problemspace = \fill, partspace = 1.5in, showdata = true, warnreuse = 120}

\begin{document}
\begin{questions}
\useproblem{calculus/derivatives/chain-rule-01}
\useproblem[partspace = {1in, 2.5in}]{calculus/derivatives/product-rule-01}
\useproblem[newpage, spacestyle = lines, points = 10]{calculus/integrals/u-sub-01}
\end{questions}
\end{document}
```

## Usage history and the catalog

Compiling `library/catalog.tex` with LuaLaTeX:

1. finds every problem in the library,
2. reads every `.pbu` file under the `roots` folders,
3. writes `library/usage-db.tex`, which assignments read for `showdata` notes and `warnreuse`,
4. typesets the catalog with each problem's metadata and usage history.

Recompile the catalog after finishing assignments to bring the history up to date.

```latex
\documentclass{exam}
\usepackage{amsmath}
\usepackage[catalog, roots = {~/Teaching}]{problib}
\begin{document}
\problibcatalog[folders = calculus, tags = {trig}, sort = lastused]
\end{document}
```

## License

Released under the LaTeX Project Public License, version 1.3c. See [`LICENSE`](LICENSE).
