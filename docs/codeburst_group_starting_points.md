# Code Burst Breakout Group Starting Points

This guide provides suggested starting points for each Code Burst breakout group. The goal is to help participants move from an idea to a small, testable prototype using the spec-driven row-wise workflow.

The purpose of the activity is not to build a perfect final product. The goal is to define a useful input, a bounded decision or extraction task, and a structured output that a human can review.

## Recommended workflow for all groups

1. Start with 5 to 10 example rows.
2. Define what one row represents.
3. Define the decision, classification, or extraction task.
4. Define the expected structured output.
5. Run the notebook.
6. Inspect the results.
7. Refine the specification before scaling.

## Breakout group sections

1. [Biomedical Concept Extraction + Normalization](#1-biomedical-concept-extraction--normalization)
2. [Metadata Structuring + Harmonization](#2-metadata-structuring--harmonization)
3. [Issue Triage + Human Validation](#3-issue-triage--human-validation)
4. [HEAL Publications + Dataset Discovery](#4-heal-publications--dataset-discovery)

---

## 1. Biomedical Concept Extraction + Normalization

### Use case

This group focuses on extracting biomedical concepts from freeform text, such as patient descriptions, case summaries, or clinical-style notes.

The workflow could extract relevant demographics, phenotypes, diagnoses, symptoms, exposures, medications, or other biomedical concepts. These extracted concepts may then be normalized into CURIEs or ontology-like identifiers using tools such as Name Resolver or Node Normalizer.

### Helpful starting question

What specific concepts should the workflow extract from the text, and what should count as relevant enough to normalize?

### Suggested inputs

Start with a small dataset where each row contains one freeform text description.

| Field | Description |
| --- | --- |
| `record_id` | Unique row identifier |
| `patient_description` | Freeform patient or case description |
| `comparison_terms` | Optional known terms to compare against |
| `notes` | Optional reviewer notes |

### Suggested extraction categories

Define extraction categories before writing or modifying the prompt.

| Category | Examples |
| --- | --- |
| Demographics | age, sex, race or ethnicity, location |
| Clinical phenotypes | chronic pain, neuropathy, fatigue |
| Diagnoses | fibromyalgia, opioid use disorder |
| Medications or exposures | opioid therapy, NSAIDs, surgery |
| Social or contextual factors | housing instability, employment status |

### Suggested output structure

A first prototype could return structured extracted fields such as:

```json
{
  "extracted_terms": [
    {
      "text": "chronic low back pain",
      "category": "phenotype",
      "evidence_quote": "patient reports chronic low back pain",
      "normalization_ready": true
    }
  ],
  "terms_to_exclude": [],
  "review_flag": "Needs review"
}
```

### Good first win

Get the notebook to reliably extract a clean list of terms from each row, including category labels and evidence quotes. The CURIE normalization step can be treated as a second layer once extraction is stable.

### Ways to go forward

Once the first prototype is working, this group could explore:

- Adding controlled categories for concept type.
- Returning one term per row or multiple terms per row.
- Adding evidence quotes so reviewers can verify each extraction.
- Adding a `normalization_ready` flag.
- Comparing extracted terms against a known reference list.
- Creating a final unique concept list across all rows.
- Separating explicitly stated concepts from inferred concepts.
- Adding review flags for vague, ambiguous, or conflicting descriptions.

### Helpful collaborators

This group would benefit from collaborators who understand:

- Biomedical ontologies or CURIEs.
- The source text and what counts as a meaningful concept.
- JSON schema design.
- Validation and false positive review.

---

## 2. Metadata Structuring + Harmonization

### Use case

This group focuses on workflows that convert messy, freeform, or inconsistent metadata into a more structured format. It may also include workflows that evaluate where variables, forms, or full data dictionaries could be harmonized across datasets.

This group may include two related directions:

1. Biological model metadata structuring.
2. Data dictionary harmonization review.

### Helpful starting question

What structure do we wish this messy metadata already had?

### Suggested inputs

#### Biological model metadata

For biological model metadata, each row might represent one model description.

| Field | Description |
| --- | --- |
| `model_id` | Unique model identifier |
| `model_description` | Freeform biological model description |
| `source` | Optional source of the description |
| `notes` | Optional reviewer notes |

#### Data dictionary harmonization

For data dictionary harmonization, each row might represent one variable, form, or data dictionary element.

| Field | Description |
| --- | --- |
| `variable_name` | Original variable name |
| `form_name` | Form, survey, or instrument name |
| `field_label` | Variable label or question text |
| `response_options` | Permissible values, coding, or response choices |
| `notes` | Optional reviewer notes |

### Suggested schema fields

#### Biological model metadata

A first prototype could extract structured fields from each model description.

| Field | Example |
| --- | --- |
| `model_type` | mouse model, organoid, cell line |
| `organism` | Mus musculus |
| `disease_context` | neuropathic pain |
| `intervention` | drug treatment |
| `assay_type` | behavioral assay |
| `missing_fields` | strain, sex |
| `source_text_evidence` | quote from the description |

#### Data dictionary harmonization

A first prototype could focus on one level of harmonization.

| Level | Possible question |
| --- | --- |
| Variable-level | Does this variable resemble a known concept? |
| Form-level | Does this form resemble a known instrument or questionnaire? |
| Dataset-level | How reusable or harmonizable is this data dictionary overall? |

### Suggested output structure

#### Example output for biological model metadata

```json
{
  "model_type": "mouse model",
  "organism": "Mus musculus",
  "disease_context": "chronic pain",
  "key_assay": "behavioral pain sensitivity",
  "missing_fields": ["strain", "sex"],
  "source_text_evidence": "mouse model of chronic pain using behavioral pain sensitivity testing",
  "review_flag": "Needs review"
}
```

#### Example output for data dictionary harmonization

```json
{
  "candidate_harmonization_level": "Variable-level",
  "potential_match": "Pain intensity",
  "confidence": "Medium",
  "rationale": "The field label asks about pain severity using a numeric scale.",
  "review_flag": "Needs human review"
}
```

### Good first win

Pick one narrow schema or one harmonization level first.

Avoid trying to structure metadata, harmonize variables, summarize forms, and assess full data dictionaries all at once. A strong first prototype should focus on one clear row-level decision or extraction task.

Examples of good first wins:

- Extract structured fields from 5 to 10 biological model descriptions.
- Identify missing metadata fields that would be needed for reuse.
- Flag variables that may map to a known concept.
- Generate a small human-review table with candidate matches and rationale notes.

### Ways to go forward

Once the first prototype is working, this group could explore:

- Defining required vs optional metadata fields.
- Identifying missing metadata needed for reuse.
- Creating review flags for incomplete or ambiguous descriptions.
- Comparing freeform descriptions against a controlled schema.
- Generating a cleaned metadata table.
- Creating candidate harmonization matches with confidence levels.
- Separating high-confidence matches from possible matches.
- Producing a human-review worksheet with rationale notes.
- Summarizing harmonization potential at the variable, form, or dataset level.
- Designing a validation workflow where humans can accept, reject, or revise suggested matches.

### Helpful collaborators

This group would benefit from collaborators who understand:

- The target metadata schema.
- Biological model descriptions.
- Data dictionaries or harmonization workflows.
- Examples of good vs incomplete metadata.
- Review flag logic.
- Human validation workflows.

### Suggested prototype framing

By the end of the activity, this group could aim to produce a small review-ready table that takes messy metadata as input and returns structured fields, missing-field flags, candidate harmonization labels, confidence levels, and rationale notes for human review.

---

## 3. Issue Triage + Human Validation

### Use case

This group focuses on workflows that classify, prioritize, and review GitHub issues or other task-tracking records.

The goal is not to fully automate maintainer decisions. The goal is to use a specification-driven LLM workflow to produce structured triage suggestions that a human can quickly review, confirm, correct, or send back for more information.

Example use cases:

- Classify GitHub issues by priority, impact, size, or component.
- Identify which issues need more information before they can be acted on.
- Suggest next steps for maintainers.
- Compare LLM-generated labels against existing labels or human corrections.
- Create a validation interface where reviewers can accept, reject, or revise model outputs.

### Helpful starting question

What decisions do maintainers repeatedly make when reviewing a new issue?

Examples:

- How urgent is this issue?
- What part of the system does it affect?
- How much work might it take?
- Is the issue actionable?
- Does the issue need more information?
- What should happen next?

### Suggested inputs

Start with a small export of GitHub issues or issue-like records. Each row should represent one issue.

| Field | Description |
| --- | --- |
| `issue_id` | Unique issue identifier |
| `issue_title` | Short issue title |
| `issue_body` | Full issue description |
| `existing_labels` | Current GitHub labels, if available |
| `comments` | Optional comments or discussion |
| `created_date` | Optional issue creation date |
| `repository` | Optional repository name |
| `component_hint` | Optional known or suspected component |

Start with 10 to 20 issues so the group can inspect the outputs manually before scaling.

### Suggested labels

A strong first prototype should define bounded label options before running the model.

#### Priority

- Critical
- High
- Medium
- Low
- Not enough information

#### Impact

- Blocks use
- Degrades use
- Cosmetic or minor
- Enhancement request
- Unknown

#### Size

- Small
- Medium
- Large
- Needs scoping

#### Component

- UI
- API
- Documentation
- Data ingestion
- Authentication
- Infrastructure
- Unknown

#### Actionability

- Actionable
- Needs more information
- Duplicate
- Out of scope
- Needs maintainer review

### Suggested output structure

A first prototype could ask the LLM to return structured JSON like this:

```json
{
  "priority": "High",
  "impact": "Blocks use",
  "size": "Medium",
  "component": "Data ingestion",
  "actionability": "Needs more information",
  "recommended_next_step": "Request logs and reproduction steps",
  "rationale": "The issue describes a failed import that prevents users from completing the workflow, but it does not include logs or a reproducible example.",
  "review_flag": "Review recommended"
}
```

### Good first win

Get reliable triage suggestions for 10 to 20 issues using a clear label set.

A good first prototype should produce a review-ready table with:

- Original issue title.
- Original issue body.
- Suggested priority.
- Suggested impact.
- Suggested size.
- Suggested component.
- Suggested next step.
- Rationale.
- Review flag.

Avoid starting with model comparison or neural network benchmarking on day one. First, define what good labels look like and create a small set of human-reviewed examples.

### Ways to go forward

Once the first prototype is working, this group could explore:

- Comparing LLM labels against existing GitHub labels.
- Adding a `needs_more_information` output.
- Adding suggested maintainer responses.
- Identifying duplicate or related issues.
- Creating a triage-ready spreadsheet.
- Creating a correction interface or command-line quiz.
- Using reviewer corrections as future training data.
- Comparing LLM triage against a neural network or supervised classifier.
- Measuring agreement between the model and human reviewers.
- Refining label definitions based on validation results.

### Helpful collaborators

This group would benefit from collaborators who understand:

- The repository and its issue history.
- How maintainers currently triage issues.
- Label definitions and issue taxonomy.
- Human validation workflows.
- Review interface or quiz-style correction workflows.
- Evaluation metrics for classification tasks.

### Suggested prototype framing

By the end of the activity, this group could aim to produce a small review-ready issue triage table and, optionally, a lightweight validation quiz that lets maintainers rapidly confirm, reject, or revise model-generated labels.

The most valuable output may not be the model’s first answer. The most valuable output may be the reviewed correction log, because that can show where the specification needs improvement and where human judgment is still essential.

### Optional subsection: Validator quiz pattern

The validator quiz is a lightweight human-in-the-loop review pattern that can be used after a specification-driven LLM workflow produces structured outputs.

The purpose of the quiz is to make model validation faster, more consistent, and easier to document. Instead of asking reviewers to inspect a large spreadsheet all at once, the quiz presents one item, or one group of related items, at a time. The reviewer can then confirm, reject, revise, or skip the model’s proposed output.

This pattern is useful when a workflow produces model-generated labels, classifications, matches, extracted fields, or other structured outputs that need human review before being treated as final.

#### Core idea

The model produces a structured suggestion.

The validator quiz helps a human reviewer decide:

- Is the suggestion correct?
- Should it be rejected?
- Should it be changed to another approved option?
- Should similar future suggestions be handled the same way?
- Which decisions should be logged for later review?

The most valuable output is not only the final reviewed dataset. The correction log is also valuable because it shows where the model, prompt, label definitions, or task specification may need improvement.

#### When to use this pattern

Use a validator quiz when:

- The model output needs human confirmation.
- The task has controlled labels or approved choices.
- The same kinds of decisions repeat across many rows.
- Reviewers need a faster alternative to scanning a full spreadsheet.
- You want to capture discrepancies between model output and human judgment.
- You want to build a small gold-standard review set.
- You want human corrections that can inform future prompt refinement, evaluation, or model training.

Example use cases:

- Confirming proposed CDE or CRF matches.
- Reviewing GitHub issue priority labels.
- Validating publication dataset-use classifications.
- Confirming metadata extraction fields.
- Reviewing harmonization candidates.
- Validating biomedical concept extraction outputs.

#### High-level workflow

1. Load the model output file.
2. Identify rows that need human review.
3. Show one row, or one grouped set of rows, at a time.
4. Display the original input, model suggestion, and rationale.
5. Let the reviewer keep, reject, revise, undo, or skip.
6. Remember repeated decisions when appropriate.
7. Save final reviewed outputs.
8. Save discrepancies separately for follow-up analysis.

#### 1. Configure the review inputs

The quiz should define important file paths, column names, and approved options at the top of the script.

This makes the quiz reusable across workflows.

| Config field | Purpose |
| --- | --- |
| `INPUT_FILE` | File containing model outputs |
| `SHEET_NAME` | Sheet or tab to review |
| `ID_COL` | Unique row identifier |
| `TEXT_COL` | Main text or description shown to reviewer |
| `MODEL_OUTPUT_COL` | Model-generated label, match, or extraction |
| `RATIONALE_COL` | Model explanation or evidence |
| `APPROVED_OPTIONS` | Controlled list of valid choices |
| `OUTPUT_FILE` | Reviewed output file |

Example issue-triage version:

| Config field | Example |
| --- | --- |
| `INPUT_FILE` | GitHub issue export file |
| `SHEET_NAME` | `LLM_Output` |
| `ID_COL` | `issue_id` |
| `TITLE_COL` | `issue_title` |
| `BODY_COL` | `issue_body` |
| `MODEL_OUTPUT_COL` | `proposed_component` |
| `RATIONALE_COL` | `rationale` |
| `APPROVED_OPTIONS` | UI, API, Documentation, Data ingestion, Infrastructure |
| `OUTPUT_FILE` | Reviewed issue triage workbook |

#### 2. Define controlled review choices

The quiz should use simple commands so review can move quickly.

| Key | Meaning |
| --- | --- |
| `y` | Keep the model suggestion |
| `n` | Reject the model suggestion |
| `l` | Choose a better value from an approved list |
| `u` | Undo the last decision |
| `s` | Skip the remaining rows |

These controls keep the validation process consistent and reduce free-text variation.

#### 3. Show enough context for review

Each quiz item should show the reviewer enough context to make a decision without overwhelming them.

Helpful fields to display:

- Row number or record ID.
- Original source text.
- Model-proposed output.
- Model rationale.
- Existing label or known value, if available.
- Any relevant grouping information.

Example display:

```text
Row 42:
  Original Item        -> Import fails when uploading CSV
  Source Description   -> User reports that CSV upload fails during validation.
  Existing Label       -> bug
  Proposed Label       -> Data ingestion
  Rationale            -> The issue describes a failure during file upload and validation.

Keep? [y]es / [n]o / [l]ist / [u]ndo last / [s]kip all:
```

#### 4. Support approved option lists

When the reviewer chooses from a list, the options should come from a controlled vocabulary or approved label set.

Example:

```text
Select from these approved options:
  1. UI
  2. API
  3. Documentation
  4. Data ingestion
  5. Authentication
  6. Infrastructure
  7. Unknown
```

This keeps reviewed outputs structured and prevents inconsistent labels.

#### 5. Review grouped items when useful

Some workflows benefit from reviewing related rows together.

For example:

- Multiple variables with the same acronym.
- Multiple issues with the same proposed component.
- Multiple publications with the same dataset signal.
- Multiple extracted terms from the same source text.

Grouped review can make validation faster because one decision may apply to several related rows.

Example grouped decision:

```text
GROUP: API Errors   (6 rows)

Row 12 | Login endpoint timeout
Row 18 | Token refresh fails
Row 27 | API returns 500 on upload

Possible approved labels:
  1. API
  2. Authentication
  3. Infrastructure
  0. These are not all the same type of issue

Enter choice [0-3]:
```

If the reviewer selects an approved option, the quiz can apply that decision to the whole group.

#### 6. Auto-apply repeated decisions

The quiz can remember previous human decisions and automatically apply them when the same decision pattern appears again.

Example:

```text
Data import -> Data ingestion
```

This is helpful when:

- The same proposed match appears repeatedly.
- The same model error occurs across many rows.
- A repeated label needs consistent correction.
- Reviewers want to reduce repetitive work.

Auto-applied decisions should still be logged so the final output remains auditable.

#### 7. Include undo and skip controls

A good review tool should support lightweight recovery.

| Control | Why it matters |
| --- | --- |
| Undo last decision | Lets reviewers recover from accidental choices |
| Skip current item | Lets reviewers return to ambiguous rows later |
| Skip remaining rows | Lets reviewers stop without losing completed work |

Undo is especially helpful during live review because it reduces fear of making a mistake.

#### 8. Track discrepancies

Whenever the reviewer rejects or changes a model output, the quiz should save the row to a discrepancy log.

The discrepancy log helps answer:

- Where was the model wrong?
- Which labels were confusing?
- Which decisions required human judgment?
- Which prompt instructions need improvement?
- Which examples should be added to the specification?

Suggested discrepancy fields:

| Field | Description |
| --- | --- |
| `row_number` | Row number from the source file |
| `record_id` | Unique record identifier |
| `original_input` | Source text or row context |
| `model_output` | Original model-generated output |
| `reviewed_output` | Human-confirmed or corrected output |
| `decision_type` | Keep, reject, changed from list, skipped |
| `decision_source` | Manual or auto-applied |
| `rationale` | Model rationale |
| `reviewer_note` | Optional reviewer note |

#### 9. Export review-ready outputs

The validator quiz should save the reviewed dataset and related review artifacts.

| Sheet | Purpose |
| --- | --- |
| `Reviewed Output` | Full dataset with final human-reviewed values |
| `Discrepancies` | Rows where the reviewer changed or rejected model output |
| `Summary` | Counts by label, review flag, or decision type |
| `Metadata` | Prompt version, model version, review date, and label definitions |

For some workflows, additional sheets may be useful:

| Sheet | Purpose |
| --- | --- |
| `Grouped Decisions` | Decisions applied to grouped rows |
| `Auto Applied` | Decisions reused from prior reviewer choices |
| `Skipped Rows` | Rows that need later review |

#### Example output record

A reviewed row might look like this:

```json
{
  "record_id": "42",
  "model_output": "Data import",
  "reviewed_output": "Data ingestion",
  "decision_type": "changed_from_list",
  "decision_source": "manual",
  "review_flag": "Human corrected",
  "reviewer_note": "Use the approved component label: Data ingestion."
}
```

#### How to recreate this quiz for a new workflow

To adapt this pattern to a new use case:

1. Identify the model output column that needs review.
2. Define the approved options or allowed labels.
3. Decide what context the reviewer needs to see.
4. Create simple review controls.
5. Decide whether repeated decisions should be auto-applied.
6. Log human corrections in a discrepancy sheet.
7. Export a final reviewed file.

The domain can change, but the validation pattern stays the same.

---

## 4. HEAL Publications + Dataset Discovery

### Use case

This group focuses on using HEAL publication metadata to identify possible dataset-use signals.

The goal is to review publication titles, abstracts, and related identifiers to flag publications that may mention or use datasets, repositories, cohorts, registries, clinical trial datasets, electronic health record data, survey data, or other reusable data sources.

This workflow is not meant to prove dataset availability from the abstract alone. Instead, it helps prioritize publications that may be worth deeper follow-up review.

### Helpful starting question

What signals in a publication suggest that a dataset may exist, was reused, or should be investigated further?

### Suggested inputs

Start with the example HEAL publications dataset in the notebook. The sample dataset is intentionally small so the workflow is easy to run, inspect, and revise.

Each row should represent one publication.

| Field | Purpose |
| --- | --- |
| `PMID` | PubMed identifier |
| `PMCID` | Full-text availability signal |
| `DOI` | Publication link or identifier |
| `Title` | Main classification input |
| `Abstract` | Main classification input |
| `Grant Number` | Optional HEAL matching signal |
| `Journal` | Optional publication context |
| `Publication Year` | Optional timing/context field |

### Suggested dataset-use signals

A first prototype can focus on identifying whether the publication contains signals that data may exist, data were reused, or data may be available through another source.

| Signal | Example |
| --- | --- |
| Existing dataset use | We analyzed data from... |
| Repository mention | dbGaP, PhysioNet, ICPSR, NDA |
| Registry or cohort use | National registry, longitudinal cohort |
| Clinical trial dataset | Secondary analysis of trial data |
| EHR or claims data | Electronic health records, Medicaid claims, insurance claims |
| Survey dataset | National survey, longitudinal survey, population survey |
| Data availability statement | Data are available at... |
| Original data collection only | Primary data collection with no reuse signal |
| No dataset signal | No dataset, repository, cohort, or reuse signal identified |

### Suggested labels

To keep the model bounded, define allowed labels before running the workflow.

#### Publication type

- Empirical research article
- Review or commentary
- Protocol or methods paper
- Editorial or perspective
- Case report
- Not enough information

#### Existing data use

- Likely existing dataset use
- Possible existing dataset use
- No existing dataset use identified
- Not enough information

#### Dataset signal type

- Repository
- Registry
- Cohort
- Clinical trial dataset
- EHR or clinical records
- Claims data
- Survey dataset
- Administrative data
- Data availability statement only
- No dataset signal identified
- Not enough information

#### Review flag

- Ready for review
- Needs human review
- Needs full-text review
- Low confidence
- No follow-up needed

### Suggested output structure

A first prototype could ask the LLM to return structured JSON like this:

```json
{
  "publication_type": "Empirical research article",
  "existing_data_use": "Possible existing dataset use",
  "dataset_signal_type": "Cohort or registry",
  "dataset_or_source_mentioned": "National survey dataset",
  "evidence_quote": "We analyzed data from...",
  "confidence": "Medium",
  "review_flag": "Needs human review"
}
```

### Good first win

Create a review-ready spreadsheet that flags which publications are worth investigating further.

A good first prototype should produce:

- Publication identifier.
- Publication title.
- Publication type.
- Existing dataset-use classification.
- Dataset signal type.
- Dataset or source mentioned, if available.
- Evidence quote.
- Confidence level.
- Review flag.

The most useful output is a prioritized list of publications for human follow-up, not a final determination of dataset availability.

### Ways to go forward

Once the first prototype is working, this group could explore:

- Separating dataset mentioned from dataset used as the primary analytic source.
- Capturing evidence quotes from the abstract.
- Adding links to PMID, PMCID, or DOI.
- Flagging publications that need full-text review.
- Comparing PubMed grant numbers against internal HEAL records.
- Identifying publications that mention repositories.
- Identifying publications that appear to use secondary data analysis.
- Creating outreach candidate lists.
- Creating a dataset discovery lead score.
- Tracking whether a publication points to a known HEAL study or dataset.
- Distinguishing between original data collection and reuse of existing data.
- Adding a human validation step to confirm or correct model classifications.

### Suggested human review questions

After the model produces outputs, reviewers can ask:

- Does the abstract clearly describe use of an existing dataset?
- Is the dataset named directly, or only implied?
- Is the publication using data as the primary analytic source?
- Does the publication mention a repository, registry, cohort, or trial?
- Is full-text review needed to confirm the dataset signal?
- Is this publication a good candidate for data discovery follow-up?
- Is this publication a good candidate for outreach?

### Suggested review-ready output table

A final spreadsheet could include columns like:

| Column | Description |
| --- | --- |
| `PMID` | PubMed identifier |
| `PMCID` | Full-text identifier, if available |
| `DOI` | DOI, if available |
| `Title` | Publication title |
| `Publication Type` | Model-classified publication type |
| `Existing Dataset Use` | Model-classified dataset-use signal |
| `Dataset Signal Type` | Type of dataset signal detected |
| `Dataset or Source Mentioned` | Dataset, cohort, registry, or repository name if detected |
| `Evidence Quote` | Short quote from the abstract supporting the classification |
| `Confidence` | High, Medium, or Low |
| `Review Flag` | Human review recommendation |
| `Reviewer Decision` | Optional human-confirmed decision |
| `Reviewer Notes` | Optional notes from human review |

### Helpful collaborators

This group would benefit from collaborators who understand:

- HEAL publications.
- Publication metadata.
- PubMed or publication review workflows.
- Data sharing and reuse signals.
- Repository, registry, and cohort references.
- Downstream stewardship actions.
- Human validation workflows.

### Suggested prototype framing

By the end of the activity, this group could aim to produce a small review-ready spreadsheet that identifies publications with possible dataset-use or data discovery signals.

The first-pass model output should help reviewers decide which publications are worth further investigation. The human reviewer remains responsible for confirming whether a dataset is actually available, reusable, or connected to a known HEAL study.

The most valuable outcome may be a practical lead list for follow-up review, rather than a final classification.

### Optional subsection: PubMed/PMC API enhancement

As a next step, this workflow could incorporate PubMed and PubMed Central APIs to gather richer publication metadata and, when available, full-text content.

This could help move the workflow beyond title and abstract review by allowing the prototype to check for additional dataset-use signals in sections such as:

- Methods.
- Data availability statements.
- Supplementary information.
- Acknowledgments.
- Repository or accession statements.
- Cohort, registry, or database descriptions.

Important distinction:

- PubMed is useful for retrieving citation metadata, abstracts, publication identifiers, MeSH terms, and related publication records.
- PubMed Central, or PMC, is the better route for programmatic full-text retrieval when an article has a PMCID and is available under terms that permit reuse or text mining.

This means the workflow could first use PubMed metadata to identify candidate publications, then use PMC availability to determine whether full-text review is possible.

#### Possible API-enhanced workflow

A future version of the prototype could follow this pattern:

1. Start with a list of HEAL-related publications.
2. Use PubMed metadata to retrieve or confirm:
   - PMID.
   - PMCID.
   - DOI.
   - Title.
   - Abstract.
   - Journal.
   - Publication year.
   - MeSH terms, if useful.
3. Check whether a PMCID is available.
4. If a PMCID is available, check whether full text can be retrieved through PMC.
5. If full text is available for programmatic retrieval, extract relevant sections.
6. Run the LLM workflow on the title, abstract, and selected full-text sections.
7. Flag publications with stronger evidence of dataset use, repository links, cohort references, or data availability statements.
8. Export a review-ready table for human follow-up.

#### Suggested additional fields

If using PubMed or PMC APIs, the output table could include additional fields such as:

| Field | Purpose |
| --- | --- |
| `PMID` | PubMed identifier |
| `PMCID` | PubMed Central identifier |
| `DOI` | Publication DOI |
| `PubMed Abstract Retrieved` | Indicates whether abstract text was retrieved |
| `PMCID Available` | Indicates whether the publication has a PMCID |
| `Full Text Available in PMC` | Indicates whether full text appears available through PMC |
| `Full Text Sections Reviewed` | Methods, data availability, supplements, acknowledgments, etc. |
| `Data Availability Statement Found` | Indicates whether a data availability statement was detected |
| `Repository Mention Found` | Indicates whether a repository was mentioned |
| `Dataset Accession Found` | Indicates whether an accession number, dataset DOI, or repository ID was found |
| `Needs Full Text Review` | Flags publications where abstract review is insufficient |
| `API Retrieval Notes` | Notes about missing metadata, access limits, or retrieval issues |

#### Suggested full-text review labels

If full-text retrieval is added, the workflow could use labels such as:

Full-text availability:

- PMCID available
- PMC full text available
- Abstract only
- Full text not available through PMC
- Not checked

Dataset evidence strength:

- Strong dataset-use evidence
- Moderate dataset-use evidence
- Weak dataset-use evidence
- No dataset-use evidence identified
- Needs human review

Evidence location:

- Abstract
- Methods
- Data availability statement
- Supplementary materials
- Acknowledgments
- References
- Not found

#### Suggested output structure with API-enhanced review

```json
{
  "publication_type": "Empirical research article",
  "existing_data_use": "Likely existing dataset use",
  "dataset_signal_type": "Repository",
  "dataset_or_source_mentioned": "dbGaP",
  "evidence_location": "Data availability statement",
  "evidence_quote": "Data are available through dbGaP under accession...",
  "pmcid_available": true,
  "full_text_reviewed": true,
  "confidence": "High",
  "review_flag": "Ready for human confirmation"
}
```

#### Things to keep in mind

Full-text retrieval should be handled carefully.

Not every PubMed record has full text available. Some articles only have citation metadata or abstracts. Some articles have a PMCID but may not be available for bulk download, reuse, or text mining depending on license and access terms.

For this reason, the workflow should treat full-text retrieval as an optional enrichment step, not as a guaranteed input.

A good first version can still use title and abstract review. A more advanced version can add PubMed or PMC API retrieval to strengthen dataset discovery signals and reduce the number of publications that require manual full-text review.

#### Ways this could improve dataset discovery

Adding PubMed or PMC API retrieval could help the workflow:

- Confirm publication identifiers.
- Retrieve missing abstracts.
- Link PMIDs to PMCIDs.
- Identify which publications may support full-text review.
- Search methods sections for dataset, cohort, registry, or repository references.
- Search data availability statements for repository names or accession numbers.
- Improve confidence in dataset-use classifications.
- Reduce false positives from abstract-only review.
- Prioritize publications with stronger evidence for follow-up.

---

## Cross-group guidance

All groups can use the same general structure to get started.

### 1. Define the input

What does one row represent?

Examples:

- One patient description.
- One biological model description.
- One GitHub issue.
- One publication abstract.
- One data dictionary variable.

### 2. Define the decision

What should the model decide or extract?

Examples:

- Extract biomedical terms.
- Classify issue priority.
- Identify dataset-use signals.
- Structure metadata fields.
- Flag harmonization candidates.

### 3. Define the allowed outputs

Keep the model bounded. Avoid open-ended prompts such as `analyze this`.

Examples of useful output constraints:

- Allowed labels.
- Required JSON keys.
- Confidence levels.
- Review flags.
- Rationale notes.
- Evidence quotes.

### 4. Start with 5 to 10 rows

Do not begin with the full dataset. Start small so results are easier to inspect and revise.

### 5. Inspect the outputs manually

Ask:

- Did the model follow the schema?
- Are the labels useful?
- Are the rationales grounded in the input?
- Are the review flags helpful?
- What would a human need to verify?

### 6. Improve the specification before scaling

Most improvements should happen in the specification, not by rewriting the whole notebook.

Common things to adjust:

- Prompt instructions.
- Label definitions.
- Examples.
- Required fields.
- Validation rules.
- Review flag logic.

---