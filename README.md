# spec-driven-row-wise-workflow
A specification-driven template for LLM-assisted data stewardship workflows with human-in-the-loop validation.

This workflow is designed to be **config-driven**, allowing users to adapt prompts, inputs, and outputs without modifying the core notebook.

---

## May 27, 2026 AI Code Burst Activity

This repository is also being used as the foundation for a RENCI internal AI Code Burst activity on May 27, 2026.

The Code Burst activity is a guided, notebook-based mini hackathon designed to help participants experiment with a specification-driven pattern for LLM-assisted row-wise review tasks. Participants will work from a simplified “Code Burst Edition” notebook that demonstrates how to define a bounded task, send structured row context to an LLM, parse structured JSON responses, validate outputs, and keep a human reviewer in the loop.

For the workshop example, the notebook uses a small HEAL publications dataset with the following fields:

- `PMID`
- `PMCID`
- `DOI`
- `Title`
- `Abstract`

The sample workflow demonstrates two chained LLM calls:

1. **Publication type classification**
   - The model reviews the publication title and abstract.
   - It returns a structured publication type label, confidence level, and rationale.

2. **Existing data use classification**
   - The model reviews the title, abstract, and rationale from the first LLM call.
   - It determines whether the publication appears to use pre-existing data, such as an existing dataset, database, registry, cohort, repository, electronic health record dataset, survey dataset, or trial dataset.

This activity is intended to demonstrate the reusable workflow pattern rather than produce final classifications. The emphasis is on showing how LLM-assisted review can be made more transparent, auditable, and adaptable by using:

- Human-defined task specifications
- Bounded labels
- Structured JSON outputs
- Validation checks
- Review flags
- Exportable results
- Human-in-the-loop decision points

### Code Burst Notebook Goals

By the end of the activity, participants should understand how to:

- Load a small sample or approved bring-your-own dataset
- Define which columns are sent to the model
- Create task-specific instructions and allowed labels
- Run a first LLM call on one row before scaling
- Parse and validate model outputs
- Chain a second LLM call using results from the first call
- Merge LLM outputs back onto the original dataset
- Export a review-ready Excel file

### Data Use Guardrails

For the Code Burst activity, participants should only use data that is:

- Publicly shareable
- Synthetic
- De-identified
- Approved for use in the sandbox environment

Participants should not upload or process PHI, PII, credentials, restricted datasets, private tokens, or sensitive internal administrative/personnel data.

### Sandbox Notes

The Code Burst notebook is designed to run in a Jupyter-style environment, with Azure sandbox support under discussion. Model provider access, authentication, package installation, file upload, and output download may depend on the sandbox configuration.

The workflow itself is intended to remain provider-aware but adaptable: the row-wise logic should remain stable even if the model provider, endpoint, or authentication approach changes.

---

## Configuration Overview

The workflow is designed to be **config-driven**, meaning most customization should happen in the configuration file rather than the notebook.

### Prompt Configuration

The config file is where you will define and manage your **AI prompt instructions**.

- This is where you input your task-specific prompts
- Prompts should be clear, structured, and designed to return consistent outputs (ideally JSON)

For more complex analyses, it is recommended to create **multiple prompts** that operate in sequence (e.g., refinement → classification → validation).

### Example Prompt Use Cases

See the configuration file for examples inspired by RENCI-related workflows, including:

- **HEAL CDE ID**  
  Used for identifying and standardizing common data elements across studies

- **HEAL Publication Categorization**  
  Used for classifying publications into predefined research categories

- **RADx Prompts**  
  Used for structured evaluation and categorization of RADx-related data

These examples demonstrate how multiple prompts can be used together to perform layered, row-wise evaluation tasks.

---

## Optional: Initialize logging

Set up lightweight logging to capture key workflow events such as model responses, parsing issues, and export status.

Logging is optional but recommended for debugging and traceability.  
Logs will be written to the `logs/` folder.

---

## Notebook Customization

While most workflow parameters are controlled through the config file, **some light updates to the notebook may be required** depending on your use case.

For example:
- Renaming or adding input columns
- Adjusting payload construction
- Aligning output field names with your prompts

The notebook includes **Markdown guidance throughout each step** to indicate where modifications may be needed.

---

## Key Idea

> Modify the **config file** for most changes, and use the notebook’s built-in guidance for any required structural adjustments.