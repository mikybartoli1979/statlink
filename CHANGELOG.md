# Changelog

All notable changes to statlink will be documented in this file.
The format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

## [0.1.0] - 2026-03-29

### Added
- `\tagvalue{key}{value}` command to tag values in table cells
- `\getvalue{key}` command to retrieve tagged values in text
- Built-in transforms: `expminusone`, `percent`, `abs`
- `round=N` formatting option to round to N decimal places
- `percent` formatting option to append a % sign
- Duplicate key detection at tagging time with package warning
- Graceful handling of non-numeric and blank values with transforms
- Graceful handling of unknown transform names with package warning
- Visible `??[key]??` marker and log warning for missing keys

### Known limitations
- Requires two compilations to resolve values
- Custom user-defined transforms not yet implemented
- Compatibility with `longtable` and `tabularx` not yet systematically verified

## [Unreleased]

### Planned
- Custom transform definitions via `\deftransform`
- R, Stata and Python companion helpers for fully automated pipelines
- Systematic testing across common table packages
- CTAN submission
