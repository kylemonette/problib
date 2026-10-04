# problib

A LaTeX package for keeping a library of problems and building assignments from it using the `exam` class.
Each assignment records which problems it used, and a catalog document collects that history so you can see when and where every problem was last used.

Assignments compile with pdfLaTeX, XeLaTeX or LuaLaTeX; the catalog requires LuaLaTeX.

The full manual is [`problib.pdf`](problib.pdf). Working examples are in this repository:
[`library/`](library) holds sample problems and a catalog, and [`examples/`](examples) holds a
worksheet and an exam built from them.

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

- Any metadata key is allowed; all keys appear in the catalog. `points`, `tags`, `topics` and
  `difficulty` have special uses. Brace values that contain commas or `]`.
- The body uses ordinary `exam` markup: `parts`, `subparts`, `\part[pts]`, `solution`.
- Give points on the question (`points`) or on its parts, not both, as `exam` adds them together.

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

Each `\useproblem` typesets the problem as the next `\question`. Compiling the assignment also
writes a usage record, `<jobname>.pbu`, next to it; this happens automatically, and the file never
needs editing.

### `\problibassignment{...}` (preamble)

This defines metadata of the assignment in the preamble of the file, which is accessed via the catalog.

| Key | Meaning |
|---|---|
| `name` | Assignment name (default: file name) |
| `course`, `term`, `type` | Shown in usage history |
| `date` | `YYYY-MM-DD` (default: today); used for sorting history and reuse warnings |
| `record` | `false` keeps this document out of the usage history (drafts, practice) |

### `\problibsetup{...}`

This command sets some options for the assignment, such as the path to the library and vertical spacing between parts and questions. 

| Key | Meaning |
|---|---|
| `library` | Path to the library (absolute, or relative to the document) |
| `roots` | Comma list of folders the catalog searches for `.pbu` records |
| `problemspace` | Space after a question that has no parts |
| `partspace` | Space after each part: one length, or a list used in order (last value repeats) |
| `subpartspace` | Same as `partspace`, for subparts |
| `spacestyle` | `blank`, `lines`, `dottedlines`, `box` or `grid` |
| `keyspace` | Keep the answer space when `\printanswers` is on (default `false`) |
| `showdata` | Show each problem's path, title and a table of its earlier uses above it |
| `warnreuse` | Warn when a problem was used within this many days of the assignment date |

Lengths may be anything `exam` accepts, including `\fill` and `\stretch{2}`.

Answer space goes to the innermost level: a question with parts gets `partspace` after each
part and no `problemspace`; a part with subparts gets `subpartspace` after each subpart and no
`partspace`.

### Other commands

| Command | Meaning |
|---|---|
| `\useproblem[opts]{path}` | Insert a problem. Options: `space`, `partspace`, `subpartspace`, `spacestyle`, `points`, `newpage` |
| `\problemspace[style]{length}` | Extra answer space anywhere |
| `\problemmeta{key}` | A metadata value of the current problem, for use inside a problem file |

## Usage history and the catalog

LaTeX can only write files next to the document being compiled, so each assignment keeps its
own `.pbu` record. The catalog lives in the library folder and must be compiled there, since it
treats the folder it is compiled in as the library. Compiling `library/catalog.tex` with LuaLaTeX:

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

| Option | Meaning |
|---|---|
| `folders` | Only problems under these library folders |
| `tags`, `topics` | Only problems with at least one of these (case-insensitive) |
| `difficulty` | A value (`2`) or range (`1-3`) |
| `unusedsince` | Only problems not used on or after this date |
| `sort` | `path` (default), `difficulty`, or `lastused` (never-used first) |
| `show` | Any of `metadata`, `usage`, `solution` (default: `{metadata, usage}`) |

`\problibcatalog` with no options lists every problem. It may be used several times in one
document, e.g., one section per folder.

## License

Released under the LaTeX Project Public License, version 1.3c. See [`LICENSE`](LICENSE).
