# What the telematics sources get wrong

Slidev deck on the issues in the telematics source schemas (TAF TSI 4.0.0 and Appendix E, TAP TSI B.5, B.10, B.12,
B.14, OSDM) that the telematics part of the ERA ontology inherits: the patterns behind the `xsd-improvements`
worklist with statistics, the post-processing rules and harmonisation policies of the telematics-ontology pipeline
that cope with them, and the issues that remain for the schema owners. Prepared for the European Union Agency for Railways (ERA).

**Live slides:** https://matdata-eu.github.io/slides-telematics-xsd-source-issues/

## Getting started

```bash
npm install
npm run dev
```

Open http://localhost:3030 to see the slides, and http://localhost:3030/presenter for presenter mode with speaker notes.

| Script | What it does |
|---|---|
| `npm run dev` | Live-reloading dev server |
| `npm run build` | Static site in `dist/` |
| `npm run export` | PDF export (`slides-export.pdf`, needs Playwright Chromium) |

## Publishing

- **GitHub Pages** (`.github/workflows/deploy.yml`): every push to `main` builds the deck with base path `/slides-telematics-xsd-source-issues/` and deploys it.
- **PDF release** (`.github/workflows/release.yml`): pushing a tag such as `v1.0.0` exports the slides to PDF and attaches it to a GitHub release.

## Interactive components

| Component | Slide | What it shows |
|---|---|---|
| `CategoryBars` | Eight patterns behind the 36 items | The worklist items per pattern, and how many a pipeline rule works around |
| `WrapperChain` | Containers | The TAF `ReferenceOTN` nesting, the five ontology terms it becomes, and what a reader needs |

## Where the numbers come from

| Numbers | Source |
|---|---|
| Worklist items, categories | GitLab work items labelled `xsd-improvements` in the telematics-ontology repository (9 November 2026), grouped by hand |
| XSD statistics | The schemas in `xsd-source/` of the telematics-ontology repository (named element declarations, `xs:documentation`, enumerations) |
| Definitions, wrapper classes, DP/OP pairs, code lists | Non-NeTEx bundle built by the pipeline on 7 November 2026 (`rdf-output/40-bundle/non-netex/`) |
| Rules and terms changed | Rule headers (`expects:`) on `main` 88e7c9b |
| Policies | `post-processing/policies.yaml` on `main` 88e7c9b |
| Checks, message validation | `rdf-output/50-checks/` reports of 8 November 2026 |
