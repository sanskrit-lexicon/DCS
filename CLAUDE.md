# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**DCS** (Digital Corpus of Sanskrit) is a reference repository in the Sanskrit Lexicon project. It provides corpus-level reference data for the Cologne Digital Sanskrit Dictionaries — specifically the abbreviation list, corpus bibliography, and grammatical-tag statistics exported from Oliver Hellwig's Digital Corpus of Sanskrit.

The primary purpose is not digitisation of a printed dictionary, but rather the integration of DCS-derived lexical and grammatical metadata into the Cologne Sanskrit Lexicon display infrastructure. Issues here track enhancements to how DCS data is consumed (normalised grammar tags, abbreviation resolution, new Hellwig lexical data), quality bugs (duplicate words, alien tags), and editorial questions about the DCS grammar tag schema.

Repository page: <http://kjc-fs-cluster.kjc.uni-heidelberg.de/dcs/>
Organisation: <https://github.com/sanskrit-lexicon>

## Repository Layout

| File / Directory | Role |
|---|---|
| `DCS-abbreviation-list.txt` | Tab-separated list of DCS text abbreviations (full title → short code) |
| `DCS-Corpus-Bibliography` | Free-text bibliography of DCS corpus texts with bibliographic references |
| `DCS-72034-gramm-tag-stats.csv` | CSV export of ~78 000 word–grammar-tag pairs from the DCS (# ; Word ; GRAM) |
| `CITATION.cff` | FORCE11-compliant software citation metadata (cff-version 1.2.0) |
| `LICENSE` | CC BY-SA 4.0 |
| `CONTRIBUTING.md` | Contribution guidelines (inherits org-wide standard) |
| `CODE_OF_CONDUCT.md` | Contributor Covenant 2.1 |
| `DATA_DICTIONARY.md` | Schema documentation for the CSV and abbreviation files |
| `ruff.toml` | Python linting configuration (ruff) |
| `.github/` | CI workflows, issue templates, dependabot config, PR template |

## Key Data Files

### DCS-abbreviation-list.txt

Plain UTF-8 text. Each line: `<full text name>\t<abbreviation>`. First line is a header. The list is used by the Cologne csl-app to resolve `<ls>` citation tags in other dictionaries.

### DCS-72034-gramm-tag-stats.csv

Semicolon-delimited CSV: `#;Word;GRAM`. Contains ~78 000 entries. Words are in IAST (with trailing space). GRAM is a DCS grammar code (e.g., `ind`, `n`, `m`, `adj`). Alien tags (e.g., `djan`, `djma`) are grammar codes not in the standard DCS schema — see issue #2.

### DCS-Corpus-Bibliography

Plain UTF-8 text. Free-form bibliographic entries, one per text, following the pattern:
```
<title> <year> — (<abbreviation>) <full bibliographic entry>
```

## Correction Pattern

This repo does not use `updateByLine.py` directly (there is no single compiled XML to patch). Corrections are made directly to the data files in a pull request, with the issue number referenced in the PR body.

For future integration work that produces XML change files for other dictionaries based on DCS data, the standard `updateByLine.py` pattern applies:
```sh
python updateByLine.py <input_file> <changefile> <output_file>
```

## Dependencies

- Python 3.x (for any processing scripts)
- `ruff` (linting, configured in `ruff.toml`)
- GitHub Actions CI (see `.github/workflows/`)

## GitHub Issue Conventions

All issues must have:

- **Type**: exactly one label (see table below)
- **Severity**: exactly one label (minor, medium, hard)
- **Milestone**: one of the four standard milestones

### Type labels (color `#0075ca`)

| Label | When to use |
|---|---|
| `link-target` | Building click-throughs from `<ls>` abbreviations to scanned PDF pages |
| `link-splitting` | Splitting combined `SOURCE N,N` refs into individual per-page links |
| `markup` | Normalising XML tag content (`<ls>`, `<lex>`, `<ab>`, etc.) |
| `text-correction` | Corrections to definitions or Sanskrit headwords |
| `content-enhancement` | New material, display upgrades, structural additions beyond correction |
| `encoding` | SLP1/AS/IAST transcoding, character rendering, hyphen/dash normalisation |
| `scan-quality` | Replacing blurry, skewed, or missing scan pages |
| `bug` | Broken links, XML errors, broken download files, duplicate entries |
| `question` | Scholarly or editorial questions requiring research before any code change |

### Severity labels

| Label | Color | When to use |
|---|---|---|
| `minor` | `#e4e669` | Targeted, self-contained fix |
| `medium` | `#fbca04` | Standard unit of work — one index, a batch of corrections |
| `hard` | `#d93f0b` | Large effort spanning many sources, files, or dictionaries |

### Milestone → type mapping

| Milestone | Types |
|---|---|
| Dictionary to Book | `link-target`, `link-splitting` |
| Digitization Quality | `scan-quality`, `encoding`, `bug`, `text-correction` |
| Structured Data | `markup`, `question` |
| Major Enhancements | `content-enhancement` |

## Data Format

### Grammar tag CSV (`DCS-72034-gramm-tag-stats.csv`)

| Column | Role | Example |
|---|---|---|
| `#` | Row number (1-indexed) | `1` |
| `Word` | Sanskrit word in IAST with trailing space | `a ` |
| `GRAM` | DCS grammar code | `ind` |

Common grammar codes: `ind` (indeclinable), `n` (neuter), `m` (masculine), `f` (feminine), `adj` (adjective), `iic` (ifc compound first member), `ifc` (ifc compound final member).

Alien tags (not in standard DCS schema) include codes like `djan`, `djma` — these are under investigation in issue #2.

### Abbreviation list (`DCS-abbreviation-list.txt`)

Tab-separated:
```
Abhidharmakośa	Abh
Abhidharmakośabhāṣya	AbhBh
Acintyastava	AcTa
```

First line is the header: `DCS abbreviations (Gasuns edition)`.

### Annotated CSV example

```
#;Word;GRAM
1;a ;ind         <- row 1, word "a" (Sanskrit prefix), grammar code "ind" = indeclinable
2;aka ;n         <- "aka" as neuter
3;akac ;m        <- "akac" as masculine (Paninian suffix form)
4;aka?uka ;adj   <- uncertain form "aka?uka" (? = unclear character), grammar code "adj"
```
