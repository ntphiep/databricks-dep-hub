# Databricks DE Professional Study Hub content schema

Static site in `src/`, built by `node build.mjs` into `dist/`. Exam config (sections, objectives, topics, mock sizes, glossary categories) lives in `src/site.json`. Content is JSON under `src/data/`.

The official exam guide of 2026-10-09 is the source of truth. A copy lives in `research/exam-guide-2026-10-09.txt` and `.pdf`. Online: https://www.databricks.com/sites/default/files/2026-10/Databricks-Certified-Data-Engineer-Professional-Exam-Guide-9-Oct-2026.pdf

Every author must pass two checks before handing content over.

- `python3 -I validate.py <files>` prints OK.
- `node build.mjs --check <topicId>` prints 0 layout warnings when the topic has diagrams. Then open `preview/<topicId>.png` with the Read tool and look at every diagram. Fix anything cramped, tangled, or hard to read.

## Exam facts

- 60 scored multiple-choice questions plus possible unscored ones, 120 minutes, 200 USD, online proctored or test center, valid 2 years.
- The guide renames several products. Use the guide names: Lakeflow Pipelines and Apache Spark Declarative Pipelines (formerly Delta Live Tables / DLT), Lakeflow Jobs (formerly Workflows), Declarative Automation Bundles (formerly Databricks Asset Bundles), OpenSharing (D2D and Databricks-to-Open), AUTO CDC (formerly APPLY CHANGES), Databricks Git folders (formerly Repos). Verify each rename in the docs and give the former name once.

| Section | Name | Weight |
|---|---|---|
| S1 | Developing Code for Data Processing using Python and SQL | 23% |
| S2 | Data Ingestion & Acquisition | 12% |
| S3 | Data Manipulation | 12% |
| S4 | Monitoring and Alerting | 10% |
| S5 | Cost & Performance Optimization | 15% |
| S6 | Ensuring Data Security and Compliance | 8% |
| S7 | Data Governance | 5% |
| S8 | Debugging and Deploying | 10% |
| S9 | Data Modeling | 5% |

Objective keys such as `1.7` follow the bullet order of each section in the guide; the full list is `tasks` in `src/site.json`.

The reader already works with Databricks. Skip basics (what Delta Lake, a notebook, a cluster or medallion layers are). Spend the space on what the exam tests and on advanced, recent behavior: exact semantics, defaults, limits, version requirements, renamed features, edge cases, and the decision rule between close alternatives.

Primary sources: docs.databricks.com (AWS docs are fine; note when Azure or GCP differs), databricks.com/blog, Databricks release notes, Databricks Academy, the Databricks YouTube channel and Data + AI Summit talks, Delta Lake and Apache Spark docs.

## Reader and writing rules

- **Language.** Every title and explanation is Vietnamese, including topic summary, section title, block title, callout title, legend, caption, why and explain.
- **English terms.** Product names, feature names, IT terms and exam terms stay in English inside the Vietnamese text and are never translated. The list `VI_TERMS` in `validate.py` holds Vietnamese translations the validator rejects.
- **Titles.** A section title is a short Vietnamese phrase that carries English terms, never a full English sentence. A title made only of English terms stays as is.
- **Exam phrases.** The field `decision.says`, the quiz stem and options, and the card front stay English because the exam is English.
- **Bullets, not prose.** Anything with 2 or more facts is a `- ` list, one fact per line. Every non-bullet line is at most 30 words (caption 20). Use `**bold**` for the key term in a bullet.
- **No filler.** Delete UI instructions, intros, summaries and restated titles. The validator rejects UI verbs such as "click".
- **Concise and visual.** A section holds 1 visual and at most 2 callouts. Prefer tables, compare cards, decision tables and short code examples. Use diagrams for real flows such as medallion layers, CDC, streaming state or CI/CD.
- **Exam-oriented.** Tie each concept to the exam phrasing ("MOST cost-effective", "LEAST operational overhead", "minimal code") and to the decisive signal in a scenario.
- No emoji, no em-dash, no arrows "->" in prose.

## Sources, accuracy and freshness

Every fact is checked, current and traceable. Memory and training data are hypotheses only.

- **Today is 2026-11-04.** Verify every feature name, default, limit and behavior against the current official docs. Use the exam guide names, and give the former name once, for example "Declarative Automation Bundles (formerly Databricks Asset Bundles)". Mark changes from 2025 to 2026 with callout tone `new`.
- **Open every link you cite** with WebFetch and confirm the page says what you cite it for. A link you did not open is never cited. For a YouTube video, confirm title and channel through `https://www.youtube.com/oembed?url=<video url>&format=json`.
- **Cross-check with several sources.** Official docs are the primary source. Add independent sources that add exam value: official blogs and release notes, conference talks and YouTube walkthroughs from the vendor or known practitioners, reputable practitioner blogs, exam-experience write-ups and community discussions (Reddit, Medium, Microsoft Tech Community, Databricks Community, ExamTopics discussions). Use them for exam tips, traps and what candidates report, and cross-check any technical claim they make against the docs.
- **When sources disagree**, follow the current official docs and say so in a `trap` callout when the old behavior still circulates.
- **Validator rules.** Each section cites at least 1 source. Each topic cites at least 3 distinct hosts.
- **Hand-over note.** List every claim you could not confirm in an official source, with the reason. Do not publish such a claim as fact; drop it or label it "chưa xác minh" in the text.

## Resources file `src/data/resources.json`

A curated list shown on the page "Nguồn học". Only sources you opened and checked.

```json
[{"group":"Chính thức","md":"optional 1 Vietnamese line","items":[{"label":"Exam guide","url":"https://...","kind":"official","note":"1 short Vietnamese line: what it gives the candidate","date":"2026-10"}]}]
```

- Field `kind` is one of `official`, `docs`, `course`, `practice`, `video`, `blog`, `book`, `community`, `repo`.
- Suggested groups: Chính thức; Khoá học và đề luyện; Video; Blog và kinh nghiệm thi; Cộng đồng.
- Field `date` is the publish or update date (YYYY, YYYY-MM or YYYY-MM-DD) when the page shows one. Prefer material from 2025 to 2026 that matches the current outline; label older material in `note`.

## Topic file `src/data/topics/<id>.json`

```json
{"id":"<topic id>","title":"<as in src/site.json>","domain":["<keys>"],"icon":"<icon>",
 "summary":"<1 or 2 Vietnamese sentences: which question types this chapter answers>",
 "sections":[{"id":"<id>","title":"<Vietnamese title>","icon":"<icon>","task":"<key of tasks>","blocks":[]}]}
```

The field `task` names the exam objective the section covers. Every objective assigned to the topic is covered by at least one section.

## Block types

All blocks accept `"src":[{"label":"...","url":"https://..."}]`.

1. `text` `{"md"}` at most 110 words; never two text blocks in a row.
2. `callout` `{"tone":"tip|trap|new|exam","title","md"}`
3. `diagram` uses auto layout and has NO coordinates.
   ```json
   {"type":"diagram","title":"...","caption":"...","dir":"RIGHT",
    "groups":[{"id":"aws","label":"AWS","kind":"cloud"}],
    "nodes":[{"id":"kb","icon":"bedrock","label":"Knowledge Base","sub":"RAG","group":"aws","info":"md shown on click"}],
    "edges":[{"id":"e1","from":"user","to":"kb","label":"question","style":"solid|dashed|double","tone":"primary|backup|deny|data"}],
    "legend":[{"style":"dashed","tone":"backup","label":"..."}],
    "steps":[{"title":"1. ...","md":"...","hl":["e1","kb"]}]}
   ```
   - A group kind is one of `cloud`, `region`, `vpc`, `subnet`, `account`, `org`, `onprem`, `ou`, `workspace`, `catalog`, `zone`, `external`, `service`.
   - A diagram has at most 9 nodes and 10 edges.
   - Node labels are at most 18 chars, subs 22, edge labels 22, group labels 34.
   - Rendered width stays at most 1000 px and height at most 760 px.
   - `python3 -I validate.py --icons` lists the icons. Use real product icons for products: `dbx-<product>` are official Databricks product icons (for example `dbx-unity-catalog`, `dbx-auto-loader`, `dbx-lakeflow-jobs`, `dbx-spark-declarative-pipelines`, `dbx-liquid-clustering`, `dbx-system-tables`, `dbx-asset-bundles`, `dbx-sql-warehouse`, `dbx-delta-sharing`, `dbx-photon`), `logo-<name>` are the Databricks, Delta Lake, Apache Spark, Apache Iceberg, Unity Catalog, MLflow, Delta Sharing and Terraform logos, `az-<service>` are Azure Architecture Icons and `aws-<service>` are AWS Architecture Icons. Tabler outline icons such as `users`, `clock`, `bug`, `key` remain for generic concepts that have no product icon.
   - The field `legend` is required when the diagram uses 2 or more (style, tone) pairs.
4. `table` `{"title","columns":[...],"rows":[[...]]}`
5. `decision` `{"title","rows":[{"says":"exam phrase in English","pick":"...","why":"Vietnamese, max 30 words","src":{"label","url"}}]}`
6. `code` `{"title","lang":"text|json","code","notes":[{"line":3,"md":"..."}]}` fits a prompt template example.
7. `chart` `{"title","chart":"bar|hbar|line","labels","series":[{"name","data"}],"xLabel","yLabel","note"}` takes numbers from `src`; illustrative charts say so in `note`.
8. `compare` `{"title","items":[{"name","icon","tag","points":[...]}]}` holds 2 to 4 cards.
9. `steps` `{"title","items":[{"title","md"}]}`
10. `figure` shows a real image: an official architecture diagram, a docs illustration, or a UI screenshot from the official docs.
   ```json
   {"type":"figure","kind":"diagram|screenshot|docs","title":"<Vietnamese>","img":"figs/<file>.png or https://<vendor host>/<image>",
    "alt":"<what the image shows>","caption":"<Vietnamese, max 40 words: what to look at for the exam>",
    "credit":{"label":"<site>: <page title>","url":"https://<page that shows the image>","license":"CC BY 4.0","licenseUrl":"https://creativecommons.org/licenses/by/4.0/"}}
   ```
   - **Only real images.** Take the image from the official page that shows it. Never draw, generate, crop or edit an image.
   - **Copy or embed by license.** The Databricks Website Terms of Use forbid copying images from docs.databricks.com, so embed them by their https url on docs.databricks.com or www.databricks.com, no license field, and never download them into the repo. Microsoft Learn pages for Fabric (MicrosoftDocs/fabric-docs, CC BY 4.0) and AWS docs (CC BY-SA 4.0) may be copied into `src/figs/`.
   - Open every image with the Read tool before using it, and confirm it shows what the caption says and matches the current product UI and naming.
   - Choose images that teach an exam point: object models, architecture overviews, UI pages that the exam names (settings, monitoring hubs, lineage views). Skip decorative images.
   - A section may hold 1 figure in addition to its 1 visual. A topic holds 3 to 8 figures.

## Quiz `src/data/quiz/<topicId>.json`

Each question is `{"id","topic","domain","task","source":"original","select","stem","options":[{"k","t"}],"answer":[...],"explain","wrong":{},"refs":[...]}`. Field `domain` is a key of `domains` and field `task` a key of `tasks` in `src/site.json`.

- **Stem.** English, in the style of the official sample questions: a concrete scenario of 40 to 120 words with the constraint that decides the answer (latency, cost, minimal code, governance, scale). Ends with a direct question. Code snippets are allowed in the stem with `\n` line breaks and backticks.
- **Style models.** Study the official sample questions, the official practice assessment, YouTube walkthroughs, blogs and community discussions to match difficulty and wording. Write every stem and option yourself.
- **Option count.** A question with `select` 1 has exactly 4 options, `select` 2 has exactly 5, `select` 3 has exactly 6.
- **Distractors.** Every option is plausible: a real feature used in the wrong place, an outdated approach, or a setting that breaks the constraint. Do not make the longest option the correct one.
- **Explain.** Vietnamese bullets with the decisive signal in the stem and why the answer meets it. Field `wrong` gives the specific flaw of each distractor in Vietnamese.
- **Refs.** Field `refs` points to the docs page that proves the answer.

## Cards `src/data/cards/<topicId>.json`

Each card is `{"id","front":"English exam phrase or concept","back":"English answer plus 1 Vietnamese sentence","ref":{"label","url"}}`.

## Glossary `src/data/glossary-parts/<topicId>.json`

```json
{"id":"auto-loader","term":"Auto Loader","full":"","aliases":["cloudFiles"],"cat":"<one value of glCats in src/site.json>",
 "what":"<1 Vietnamese sentence, max 25 words>",
 "means":["<2 or 3 Vietnamese bullets, max 20 words each: practical effect and what the exam asks>"],
 "related":["<ids in the same file>"],
 "src":{"label":"...","url":"https://..."}}
```

The site underlines the first occurrence of `term` or an alias in each section, so pick aliases that appear in real text. Skip terms every data engineer knows, such as database and API.
