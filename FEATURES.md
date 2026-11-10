# documentationArchi 0.6.1: features and observed behaviour

Tested 2026-11-10 with release v0.6.1 (commit `6b368a7`), Archi 5.10.0, Windows 11. Automated tests: 353 passed, 4 skipped.
- ✅ verified in the exports in `export/`
- 🧪 covered only by unit tests, or needs Archi's windows (not run by hand)
- ⚠️ see §14

## 1. Export entry points and selection

| Feature | Behaviour | Status |
|---|---|---|
| Whole model | Every view, plus: overview, "Elements not in any view", "Work packages", "Labels", catalogs, indexes, "Changes since…" (if published), Elements A–Z / by type | ✅ |
| Views / folders | `--documentation.views a,b` (name or id), `--documentation.folders Views/Claims`; they combine. No WP / Labels / catalog / index pages | ✅ 03 |
| Work package(s) | First page = the WP: its description + properties (the spec) + the elements it changes. Then the views that show them, each with the full picture but describing only those elements. Then the elements that are on no view. The PDF is named after the WP | ✅ 04 |
| Closed WP exported directly | Still gives its spec | ✅ 05 |
| Selected elements / a relationship / diagram objects | Elements: a "Selected elements" page. A relationship: both ends. A note or group: its view | 🧪 |
| Formats | `html`, `pdf`, `confluence` (default: all three) | ✅ |
| Output layout | `html/` (+`images/`, `assets/`, `search.js`, `nav.js`, `style.css`); `<model>.pdf` + `assets/`; `confluence/{pages,attachments,manifest.json,upload_to_confluence.py}`. On the command line straight into the folder; the dialog adds a subfolder named after the model | ✅ |
| Re-export | Replaces only the folders it owns, swapped in at the end; a cancel or failure keeps the previous export | 🧪 |
| Final message | Missing assets, empty catalogs/indexes, "N object(s) left out by the exclusion rules" | ✅ |

## 2. View pages

| Feature | Behaviour | Status |
|---|---|---|
| Section order | description, element list, picture, properties, notes, related views, element descriptions | ✅ |
| Described once | Each element on the first page that shows it; later pages link to it | ✅ |
| Describe Here (`doc:home` = view id) | Describes the element on that view instead; the property is never printed | ✅ (menu 🧪) |
| Element entry | Description, properties, a table of relationships within the view (name + description), "Views:", "Work packages: KEY (kind)", "Other related elements" grouped by relationship type | ✅ |
| Type label | `<<Business Process>> Name`; specialization `<<Business Actor: Broker>>`, `Flow Relationship: Sync` | ✅ |
| Visual Group | Elements listed and described under the group's name; ungrouped ones first | ✅ |
| Nested without a relationship | "Part of: Core System" / "Contains: Billing Module" | ✅ |
| Junctions | Never listed; followed through: "Check claim **or** Reject claim", "Approve claim **and** Notify customer" | ✅ |
| Notes | Under "Notes", formatted | ✅ |
| View reference | Under "Related views" | ✅ |
| Print order | PrintOrder grouping on the view + associations `001…` (natural number sort); the others alphabetical; applied inside groups. **0.6.1: only the PrintOrder on that view counts** | ✅ ⚠️ |
| Sketch view | Picture + Notes: each sticky/actor/group as its name in bold + text (sticky = `content`, others = documentation) | ✅ |
| Canvas view | Picture + sticky content + sticky notes + block content | ✅ |
| Empty view | Page with a picture only | ✅ |
| Pictures | 2× resolution, clickable (HTML image map, PDF links). PrintOrder and excluded elements are not drawn | ✅ |
| Overview | Model name, purpose, properties, pages nested under the view folders (with folder descriptions); "Generated <date> from <model> – <branch> ＠ <commit>" from `.coarchi/` | ✅ |

## 3. HTML

| Feature | Behaviour | Status |
|---|---|---|
| Sidebar | Folder tree, current page highlighted, folders fold; plain list without JS; not printed; moves on top on narrow screens | ✅ (responsive 🧪) |
| Elements A–Z, Elements by type | All described elements; by type with counts | ✅ |
| Search | `search.js` index over pages and elements (name, type, description); works from disk | ✅ |
| Same page names | `applications.html` + `applications-2.html` | ✅ ⚠️ |

## 4. PDF

| Feature | Behaviour | Status |
|---|---|---|
| Cover | Model name, Generated line, start of the purpose | ✅ |
| Table of contents | Page numbers, nested by folders | ✅ |
| Bookmarks | One per page, nested by folders | ✅ |
| Header / footer | Title at the top; model name + "Page n of m" at the bottom | ✅ |
| Landscape | For views clearly wider than tall (Claim handling) | ✅ |
| `pdf cover|contents|landscape off` | Switches that part off | ✅ 06 |
| Assets | Pictures drawn. Other files and SVG link to `assets/` next to the PDF and are embedded as PDF attachments | ✅ |
| Internal links | To element descriptions and from the picture click areas (~155 in export 01) | ✅ |

## 5. Confluence

| Feature | Behaviour | Status |
|---|---|---|
| Storage-format pages | Panels → info/note/tip/warning macros; labels → status macro; code/SQL → code macro (language, theme, linenumbers); anchors | ✅ |
| Hierarchy | A page per folder holds its views. Titles are "<model> - <page>"; a duplicate gets " (2)" | ✅ |
| Attachments | View pictures and assets per page | ✅ |
| `upload_to_confluence.py` | `--url --space [--parent]`; `CONFLUENCE_TOKEN`, or `CONFLUENCE_USER`/`CONFLUENCE_PASSWORD`; matches pages by title, so a re-upload makes a new version | `--help` ✅, upload 🧪 |
| Differences | No "Generated" line; pictures not clickable | ✅ |

## 6. Description formatting (verified in HTML; panels, labels and code also in Confluence)

| Write | Result |
|---|---|
| `INFO:` `NOTE:` `TIP:` `WARNING:` at the start of a line | coloured panel up to a blank line |
| custom `panel warning Open question` | "Open question: …" becomes a warning panel |
| `<#>…</#>` | untitled note panel |
| `#risk`, `[[security]]` | label (built-in: risk red, deprecated grey, todo/draft yellow, new green). An unknown `#word` stays text. Also works in property values (`Status=#new`) |
| `Description:` `Technically:` / `bold X` | bold |
| `style … Deadline` | colour, background, size, bold, italic… |
| `[ORDERS]` `'ACTIVE'` `{client_id}` | coloured table / constant / variable |
| `<sql>…</sql>` | SQL block |
| URL, `[text](url)` | links; `[ORDERS]` alone stays a token |
| `- ` `* ` `1. ` | lists; an indented line continues the item |
| `**bold**` `*italic*` | yes; `select *` and `2*3` are left alone |
| `\| A \| B \|` + `\|---\|` | table; without the dash line it stays text |
| ```` ```java ```` / `<code lang="python">` | code blocks |
| `[[ref:id]]`, `[[ref:id\|label]]` | link with the current name or the label. An excluded target becomes the plain name; an unknown id shows red "[missing reference: id]" |
| `[[asset:path\|alt]]`, `asset:name` | inline picture or file link; a missing one shows red "[missing asset: name]" |

## 7. Rules (`documentation.rules` next to the model, preferences, or `--documentation.rules file`)

| Rule | Behaviour | Status |
|---|---|---|
| `panel` `tag` `bold` `style` `token` `heading` `sql` | see §6 | ✅ |
| `exclude label draft`; property `doc:exclude` on an element, relationship, view or folder | left out everywhere, pictures included; refs become plain text | ✅ |
| `hide property internal*` | never printed; `doc:*` properties are never printed | ✅ |
| `language cs` | Czech fixed texts and file names (`pracovni-balicky.html`) | ✅ ⚠️ |
| `text key = value` | replaces one fixed text | ✅ ⚠️ |
| `page …` / `entry …` | order of the sections or parts; leaving one out omits it | ✅ 06 |
| `catalog "T" type=A\|B* folder=… columns=name,Prop,description` | table page sorted by name, description = its first paragraph. An empty catalog is left out with a message | ✅ |
| `index tables\|variables\|constants` | a page per token type with where each token is used (descriptions, notes, canvas notes, `[T]` in SQL) | ✅ |
| `profile name folders= views= workpackages= formats= exclude= hide= language= output= publish` | named export; `--documentation.profile`; explicit CLI options win | ✅ 10–12 |
| Precedence | file → preferences → built-in defaults. **0.6.1: a rules file adds to the built-in rules; `defaults off` makes it stand alone** | ✅ ⚠️ |
| Invalid file | the export fails, naming the file and line | 🧪 |

## 8. Work packages

| Feature | Behaviour | Status |
|---|---|---|
| Link = association element↔WP (either direction) | the entry ends with "Work packages: RBWDP-20025 Portal ordering (new)"; the link is not listed among the relationships | ✅ |
| Kind of change | the association name `new`/`change`/`remove` is shown in parentheses | ✅ |
| Closed (`doc:closed`) | hidden from the WP page and the entries; a direct export still works | ✅ |
| WP page (whole model) | each open WP: description, properties, "Changed elements" with their kinds; a WP without elements is listed too | ✅ |
| Dialogs (Link, overview, Close/Reopen, Changed Since Publish, Show in tree) | Archi's windows | 🧪 |

## 9. Publishing and changes

| Feature | Behaviour | Status |
|---|---|---|
| `--documentation.publish` | writes `documentation.published` next to the model | ✅ 01 |
| "Changes since <date>" page | first after the overview in later whole-model exports. Summary line, then added/changed/removed objects with before/after for name, description, properties, relationships, a view's element list, sketch texts and a WP's changed elements | ✅ 02 ⚠️ |
| Model tree marks | Archi's windows | 🧪 |

## 10. Check Documentation (`--documentation.check`)

Findings are grouped with counts:
- no description (elements and views)
- on no view (work packages exempt)
- views without elements
- missing or ambiguous assets
- references to deleted objects
- label typos: `#rsik (did you mean #risk?)` (1 change, or 2 for words of 4+ letters)
- WPs that change nothing

Excluded objects are not checked. `--documentation.strict` makes findings fail the run. ✅ 00, 09

## 11. Assets

| Feature | Behaviour | Status |
|---|---|---|
| Location | `Assets/` next to the model; subfolders OK | ✅ |
| References | a name alone works when it is unique; case and slash direction don't matter. A file directly in `Assets/` is meant by its bare name (see §14.3) | ✅ |
| Copies in the export | `html/assets/`; PDF `assets/` + attachments; Confluence attachments | ✅ |
| Files outside `Assets` | linked-in files from outside are ignored | 🧪 |
| Add Asset… | Archi's windows | 🧪 |

## 12. Command line

| Case | Result |
|---|---|
| success | exit 0 |
| unknown view | `IllegalArgumentException: Not found in "Feature Test": view "No such view"`, non-zero exit |
| `--documentation.strict` + missing asset | export written, then `IllegalStateException: 1 asset reference(s) have no file`, non-zero |
| `--documentation.check --documentation.strict` with findings | `13 documentation finding(s)`, non-zero |

## 13. Archi windows only (not exercised; logic unit-tested)

- Documentation Preview
- Export dialog: profile list, Publish, overwrite prompt, Open Folder, progress and cancel
- Export Profile menu
- Print Order dialog
- Describe Here, Insert Reference…, Add Asset…
- Link to Work Package…, Work Packages… overview
- Check dialog
- Formatting editor: style editors, Edit as Text, Save as Project Rules
- Preferences, model tree marks

## 14. Quirks, and bugs fixed in 0.6.1

1. **Fixed in 0.6.1.** A rules file used to *replace* the whole rule set: a hand-written `documentation.rules` without the built-in lines silently lost the panels, the labels and the label typo check. Now a file adds to the built-ins, and a line for the same keyword overrides one. `defaults off` keeps the old behaviour. Save as Project Rules writes `defaults off` when the list drops a built-in. *In a work version without the fix, export 02 shows no panels or labels.*
2. **Fixed in 0.6.1.** Print order used to fall back to any PrintOrder link, so the order of "Order flow" leaked into "Applications": Customer Portal was listed first. Now only the PrintOrder on the view counts, and Applications is alphabetical. Lists of no view still use any PrintOrder.
3. **By design (now documented).** `[[asset:readme.txt]]` with both `Assets/readme.txt` and `Assets/dup/readme.txt` uses the top-level file without a warning. A top-level file's path is its bare name.
4. **Messages differ.** The export's missing-asset message names the page (`on "Order flow"`); Check names the element (`on "Place order"`).
5. **Windows command line.** Without `--launcher.suppressErrors`, a failing run hangs on the Eclipse launcher's error dialog. With it, the run exits non-zero immediately (127 observed).
6. **`language cs`** leaves ArchiMate type names in English (`<<Business Object>>`). A `text` override beats the language.
7. **`text contents = …`** renames both the overview's list heading and each view's element-list heading.
8. **A catalog and a view with the same title** give two identical entries in the contents; the file names and Confluence titles are made unique.
9. **Overview order.** Catalog and index pages are listed first, before the views, not under "Other pages".
10. **One rename touches many entries.** It marks the element, its neighbours' relationships, the view's element list and the WP as changed on the Changes page (9 "changed" for 4 edits).
