## Define the Publication Type Classification Task

Now that each publication row has been converted into structured context, we need to define the task the LLM will perform.

For this sample workflow, the model will review the `Title` and `Abstract` for each publication and classify what type of article it appears to be.

The model should only use the title and abstract provided in the row. It should not use outside knowledge or make assumptions beyond the available text.

### Row Identifier

The `PMID` is used as the row identifier so results can be traced back to the original publication.

### Input Sent to the LLM

The model receives:

- `Title`
- `Abstract`

### Review Task

Classify each publication into one publication type based on the title and abstract.

### Allowed Publication Type Labels

The model must choose one of the following labels:

- `Original Research Article`
  - Reports new research findings, often based on collected or analyzed data, experiments, observations, surveys, interviews, clinical data, or secondary analysis.

- `Review Article`
  - Summarizes and synthesizes existing literature on a topic without presenting a formal systematic review or meta-analysis.

- `Clinical Case Report`
  - Describes one or more patient cases, usually highlighting a unique diagnosis, treatment, presentation, or clinical insight.

- `Research Design and Methods`
  - Focuses on study design, protocol development, methodology, tool development, measurement approach, or analytic methods rather than reporting primary findings.

- `Opinion Piece`
  - Presents commentary, perspective, interpretation, or argument from the authors rather than reporting new empirical findings.

- `Clinical Practice Guidelines`
  - Provides evidence-based recommendations for clinical care, diagnosis, treatment, or practice.

- `Systematic Review and Meta-Analysis`
  - Uses a formal, structured process to identify, evaluate, and synthesize multiple studies. May include statistical pooling of results.

- `Letter`
  - Short communication, response, comment, or brief academic discussion. May respond to a previously published article.

- `N/A`
  - Use this when none of the listed publication types clearly apply.

### Confidence Definitions

The model must assign one confidence level for the selected publication type:

- `High`
  - The title and abstract provide clear, direct evidence for the selected publication type.
  - The article structure or stated purpose strongly matches one category.

- `Medium`
  - The title and abstract provide some evidence for the selected publication type, but there is minor ambiguity.
  - More than one category may be plausible, but one category appears more likely.

- `Low`
  - The title and abstract provide limited, vague, missing, or conflicting evidence.
  - The publication type is difficult to determine and should likely be reviewed by a human.

### Expected Structured Output

For each row, the model should return:

- `publication_type`: one of the allowed publication type labels
- `confidence`: `High`, `Medium`, or `Low`
- `rationale`: a short explanation based only on the title and abstract

### Human-in-the-Loop Reminder

This prototype is meant to support review, not replace it. Rows with low confidence, missing abstracts, or ambiguous publication types should be reviewed by a human.