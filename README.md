# Handover: compare documentation exports

This folder holds an ArchiMate test model and the documentation I exported from it with **documentationArchi 0.6.1** (an Archi plugin: HTML, PDF and Confluence output).

**Your task:**
1. **Export the same model with your own documentation export.** Use `model/feature-test.archimate` and give your tool the matching settings where it has them.
2. **Compare your output with mine in `export/`.** For every feature, check whether your version does the same, does it differently, or does it better or worse. `FEATURES.md` says how each feature behaves in documentationArchi.
3. **Compare only features that your version has enabled.** Skip a feature your version doesn't have or has switched off, and don't report it as a difference. List the skipped features at the end in one line each.
4. **Report one table:** *feature | documentationArchi | your version | same / differs (how) / better / worse*. After it, add the features your version has that documentationArchi lacks.

## What is here

| Path | Content |
|---|---|
| `model/feature-test.archimate` | The test model to export (= `feature-test.v2.archimate`) |
| `model/feature-test.v1.archimate` | An earlier state of it. Use it only if your version compares "what changed since the last publish": publish v1, then export v2 |
| `model/Assets/` | Pictures and files the descriptions reference (`[[asset:...]]`) |
| `model/documentation.rules`, `model/alt.rules` | documentationArchi's settings (formatting, catalogs, indexes, profiles; alt = Czech + other layout). Set the same up in your tool only where it has an equivalent |
| `export/NN-*/` | My outputs: `html/`, `<name>.pdf`, `confluence/` |
| `export/logs/` | My command-line logs (messages, Check findings, errors) |
| `FEATURES.md` | Every feature with documentationArchi's behaviour. ✅ = visible in `export/`; 🧪 = only in Archi's windows or unit tests (compare only if you can). §14 = quirks |

## My exports (pick the ones your version can do)

| Folder | What was exported |
|---|---|
| `01-full-v1-published` | Whole model v1, all formats, published as the baseline |
| `02-full-v2-changes` | Whole model v2, all formats; contains the "Changes since…" page |
| `03-select-views-folders` | Only the view "Applications" + the folder Views/Claims (HTML) |
| `04-workpackage` | Only work package RBWDP-20025 (HTML + PDF) |
| `05-workpackage-closed` | The closed work package RBWDP-20030 |
| `06-alt-rules-cs` | Czech texts, a different page layout, PDF without cover, contents or landscape pages |
| `08-strict-missing-asset` | Strict mode: written, but the run fails on the missing asset |
| `10-12-profile-*` | Named export profiles (developers / Sales team / work package) |
| `logs/00-check-v1.txt`, `logs/09-check-strict.txt` | Documentation check (no export) |
| `logs/07-error-unknown-view.txt` | Error for an unknown view name |

## What the model tests (so you know where to look)

- **Views:**
  - Views in folders (Sales, Sales/Legacy, Claims).
  - A visual Group.
  - An element drawn inside another one.
  - Or / And junctions.
  - A wide view.
  - An empty view.
  - Excluded views, elements, a folder and a relationship (`doc:exclude`, `#draft`).
  - A sketch view and a canvas view.
  - Notes, a view reference, a print order (PrintOrder grouping).
- **Element "Customer":** its description uses every formatting syntax: panels, labels, a label typo, lists, a table, code, links and references.
- **Element "Place order":** tokens `[TABLE]`, `{variable}`, `'CONSTANT'`, an SQL block, and every kind of asset, including a missing one and two files with the same name.
- **Work packages:** links with a kind of change (new/change/remove), a closed one, an empty one.
- **Specializations:** "Broker" on an actor, "Sync" on a relationship. Hidden properties.

**Edits from v1 to v2:**
- "Orders API" renamed to "Orders API v2".
- Core System's description changed.
- Customer Portal's Owner changed from Team B to Team C.
- "Notify customer" deleted.
- "Fraud check" added.
- A sketch sticky got its text.
