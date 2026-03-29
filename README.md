---
editor_options: 
  markdown: 
    wrap: 72
---

# statlink — Live table values in LaTeX

When writing empirical papers, it is common to copy estimated
coefficients and other statistics from tables into the text — and then
update them by hand every time the underlying estimates change.
**statlink** automates this: you tag a value once in your table, and
retrieve it anywhere in the text. Update the table, recompile, and the
text updates automatically.

## Installation

### Overleaf

Upload `statlink.sty` to your Overleaf project. No further installation
is needed.

### Local (TeX Live / MiKTeX)

Place `statlink.sty` in the same folder as your `.tex` file, or in your
local `texmf` tree so it is available to all projects.

### Load the package

Add to your preamble:

``` latex
\usepackage{statlink}
```

## Usage

### Tagging values

Wrap a table cell value with `\tagvalue{key}{value}`. The value is
typeset normally — the table looks exactly as it would without the tag.

``` latex
\begin{tabular}{lcc}
  \hline
  Variable  & Coefficient & Std. Error \\
  \hline
  Treatment & \tagvalue{coef:treatment}{0.143} & 0.032 \\
  Control   & \tagvalue{coef:control}{0.021}   & 0.018 \\
  \hline
\end{tabular}
```

Keys can be any string. We recommend a `category:name` convention (e.g.
`coef:treatment`, `pval:treatment`) to keep things organised across a
paper with many tables.

### Retrieving values

Use `\getvalue{key}` anywhere in the text:

``` latex
The estimated treatment effect is \getvalue{coef:treatment}.
```

The same key can be retrieved as many times as you like. If a key is not
found (e.g. on the first compile, or due to a typo), **statlink** prints
`??[key]??` in bold so the problem is immediately visible.

> **Note:** like standard LaTeX cross-references, statlink requires
> **two compilations** to resolve values. On the first pass values are
> written to the `.aux` file; on the second pass they are read back into
> the document.

### Transforms

Many empirical applications require a transformation of the stored value
before displaying it in the text. For example, in a
difference-in-differences log-linear regression, the percentage effect
is approximated by exp(b) - 1 rather than b itself.

Pass a `transform=` option to `\getvalue`:

``` latex
\getvalue[transform=expminusone]{coef:treatment}
```

Built-in transforms:

| Transform     | Formula    | Typical use case                        |
|---------------|------------|-----------------------------------------|
| `expminusone` | exp(b) - 1 | Log-linear DID, percentage effects      |
| `percent`     | b × 100    | Proportion stored as decimal            |
| `abs`         | \|b\|      | Displaying magnitude regardless of sign |

### Formatting

Formatting options can be combined freely with transforms:

| Option    | Example   | Effect                    |
|-----------|-----------|---------------------------|
| `round=N` | `round=2` | Round to N decimal places |
| `percent` | `percent` | Append a % sign           |

Options can be combined:

``` latex
% exp(0.143) - 1, rounded to 1 decimal place, with % sign → 15.4%
\getvalue[transform=expminusone, round=1, percent]{coef:treatment}

% Raw value rounded to 3 decimal places
\getvalue[round=3]{coef:treatment}
```

## A complete example

``` latex
\documentclass{article}
\usepackage{statlink}
\usepackage{booktabs}

\begin{document}

\begin{table}
  \centering
  \begin{tabular}{lcc}
    \toprule
    Variable  & Coefficient & Std. Error \\
    \midrule
    Treatment & \tagvalue{coef:treatment}{0.143} & 0.032 \\
    Control   & \tagvalue{coef:control}{0.021}   & 0.018 \\
    \bottomrule
  \end{tabular}
  \caption{Regression results}
\end{table}

The estimated treatment coefficient is \getvalue{coef:treatment}.
In a log-linear specification, this corresponds to an approximate
percentage effect of \getvalue[transform=expminusone, round=1, percent]{coef:treatment}.
The control group coefficient is \getvalue[round=3]{coef:control}.

\end{document}
```

Compile twice to resolve all values.

## Warnings and error handling

**statlink** is designed to fail visibly rather than silently:

-   **Key not found** — prints `??[key]??` in bold and emits a log
    warning suggesting you check whether the document has been compiled
    twice
-   **Duplicate key** — if the same key is tagged more than once in the
    document, a warning is emitted and the first value is kept
-   **Non-numeric value with transform** — if a transform is requested
    on a non-numeric or blank cell (e.g. `n.a.`), the value is printed
    as-is without crashing
-   **Unknown transform** — emits a log warning if an unrecognised
    transform name is passed

## Known limitations

-   Requires two compilations to resolve values (standard LaTeX
    behaviour for cross-references)
-   Custom user-defined transforms are not yet implemented (planned for
    v0.2)
-   Values are stored as strings; very large or very small numbers may
    be subject to the floating point precision of `\fp_eval`
    (approximately 16 significant digits)
-   Currently tested with standard `tabular` environments; compatibility
    with more complex table packages (e.g. `longtable`, `tabularx`) has
    not yet been systematically verified

## Roadmap

-   [ ] Custom transform definitions (`\deftransform`)
-   [ ] R, Stata and Python companion helpers for fully automated
    pipelines
-   [ ] Systematic testing across common table packages
-   [ ] CTAN submission

## Contributing

Contributions are very welcome! If you find a bug, have a feature
request, or want to contribute code, please open an issue or pull
request on GitHub.

This package was motivated by the needs of empirical researchers in
economics and social sciences, and we are particularly interested in
contributions that improve compatibility with common table-generation
workflows in R, Stata and Python.

## License

This package is released under the [LaTeX Project Public License (LPPL)
v1.3c](https://www.latex-project.org/lppl/).

## Author

Carlo Reggiani

## Acknowledgements

This package was developed with the unvaluable assistance of
[Claude](https://www.anthropic.com) (Anthropic).
