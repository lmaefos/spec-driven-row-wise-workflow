# spec-driven-row-wise-workflow
A specification-driven template for LLM-assisted data stewardship workflows with human-in-the-loop validation.

This workflow is designed to be **config-driven**, allowing users to adapt prompts, inputs, and outputs without modifying the core notebook.

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