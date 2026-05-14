## Define the Existing Data Use Classification Task

This second LLM call builds on the first publication type classification step.

In the first step, the model reviewed the `Title` and `Abstract` and returned a publication type, confidence level, and rationale.

In this step, the model will review:

- `Title`
- `Abstract`
- `llm_rationale` from the publication type classification step

The goal is to determine whether the publication appears to use an existing dataset, database, registry, repository, cohort, trial dataset, electronic health record dataset, survey dataset, or another pre-existing data source for analysis.

In this context, **existing data use** means the study appears to analyze data that had already been collected before this publication’s analysis, rather than collecting new original data specifically for the study.

The title and abstract should be treated as the primary evidence.  
The prior LLM rationale may be used as supporting context, but it should not override the title and abstract.

### Review Task

Classify whether the publication appears to use pre-existing data for analysis.

### Allowed Existing Data Use Labels

The model must choose one of the following labels:

- `Likely used existing data`
  - The title or abstract clearly indicates use of a pre-existing dataset, database, registry, repository, cohort, trial dataset, electronic health record dataset, claims dataset, survey dataset, or secondary analysis.
  - Examples of strong signals may include phrases such as “secondary analysis,” “retrospective database study,” “registry data,” “electronic health records,” “claims data,” “publicly available dataset,” or named existing datasets.

- `Possibly used existing data`
  - The title or abstract suggests possible use of pre-existing data, but the evidence is not fully clear.
  - The study may mention a cohort, database, records, registry, or previously collected data source, but it is ambiguous whether the authors collected the data themselves or reused existing data.

- `Likely collected original data`
  - The title or abstract suggests that the study collected new data directly for this research.
  - Examples may include recruitment, interviews, surveys, experiments, clinical assessments, participant enrollment, biological sample collection, or prospective data collection performed as part of the study.

- `Unclear`
  - The title and abstract do not provide enough information to determine whether the study used existing data or collected original data.
  - Use this when evidence is missing, vague, or conflicting.

### Expected Structured Output

For each row, the model should return:

- `existing_data_use_label`: one of the allowed existing data use labels
- `confidence`: `High`, `Medium`, or `Low`
- `rationale`: a short explanation based on the title, abstract, and prior LLM rationale

### Confidence Definitions

- `High`
  - The title and abstract provide clear evidence for the selected label.

- `Medium`
  - The title and abstract provide some evidence, but there is some ambiguity.

- `Low`
  - The title and abstract provide weak, vague, missing, or conflicting evidence.

### Human-in-the-Loop Reminder

This classification should support review, not replace it. Rows labeled `Possibly used existing data`, `Unclear`, or assigned `Low` confidence should be reviewed by a human.