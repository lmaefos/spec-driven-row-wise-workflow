## Define the Biomedical Concept Extraction Task

This LLM call reviews one row of freeform clinical text and extracts potential phenotype and disease terms.

The goal is not to diagnose or interpret the text clinically. The goal is to identify and extract specific, nameable biomedical concepts that could be normalized to a CURIE in a controlled ontology such as the Human Phenotype Ontology (HP), MONDO, or UMLS.

### What One Row Represents

Each row represents one clinical text record. This could be a patient description, case summary, clinical note excerpt, or any other freeform biomedical narrative.

### What the Model Should Extract

The model should extract terms that describe:

- **Phenotypes**: observable characteristics, clinical signs, or physical findings
  - Examples: hypotonia, bilateral hearing loss, intellectual disability, hepatomegaly, short stature
- **Diseases**: named disease entities or clinical diagnoses
  - Examples: Rett syndrome, Type 2 diabetes, Charcot-Marie-Tooth disease
- **Syndromes**: named syndromes, even if etiology is unspecified
  - Examples: Down syndrome, Williams syndrome, Marfan syndrome
- **Symptoms**: subjective reports of clinical disturbance
  - Examples: chronic fatigue, recurrent headaches, nausea
- **Findings**: objective clinical observations that suggest a phenotypic state
  - Examples: elevated creatinine, low hemoglobin, absent reflexes

### What the Model Should NOT Extract

Do not extract:

- Generic anatomical terms without clinical significance (e.g., "left hand", "right eye" without a finding attached)
- Demographic information (e.g., age, sex, race) unless it is explicitly part of a named phenotype
- Medication names, drug names, or treatment protocols
- Procedural terms (e.g., "biopsy", "surgery", "MRI") unless the result describes a phenotype
- Vague or non-specific language (e.g., "patient was seen", "results were reviewed", "follow-up needed")
- Terms that describe normal findings (e.g., "normal development", "no abnormalities detected") unless the row contains abnormal findings alongside them
- **Clinical negations of pathological states**: phrases that report the patient does NOT have a condition. These are negative findings, not phenotype or disease terms.
  - Do not extract: "no seizures", "absence of seizures", "seizure-free", "no history of seizures", "denies headaches", "no evidence of tumor", "negative for infection", "no fevers"
  - The text "she does not have a history of seizures" tells you the patient is seizure-free — do not extract "seizures" or any seizure-related term from this statement.

### Critical Distinction: "Absence of" in Clinical Text

The phrase "absence of" is used in two very different ways in clinical text. Apply the following rule before extracting any term that begins with "absence of", "no", "lack of", or a similar negation:

**Ask: Is the absent thing normally present (and therefore its absence is the finding) — or is it a pathological condition that the patient simply does not have?**

- **DO extract** when the absence describes a missing normal function, structure, or developmental milestone that the patient should have:
  - "absence of visual tracking" → extract (visual tracking is an expected developmental ability; its absence is a phenotype)
  - "absence of focusing on faces" → extract (expected infant behavior; its absence is a finding)
  - "absent thymus" → extract (the thymus should be present; its absence is an anatomical abnormality)
  - "no response to sounds" → extract (expected sensory response; its absence is a finding)

- **DO NOT extract** when the absence describes a pathological condition the patient does not have:
  - "absence of seizures" → do not extract (seizures are pathological; the patient simply does not have them)
  - "no infections" → do not extract
  - "seizure-free" → do not extract
  - "no history of seizures" → do not extract
  - "no tumors detected" → do not extract

If you are uncertain, use the confidence level: extract with Low confidence if the text could support either interpretation, and explain the ambiguity in the extraction_notes field.

### Extraction Principles

1. **Prefer specificity**: Extract the most specific term supported by the text. Prefer "bilateral sensorineural hearing loss" over "hearing loss" if the text supports it.
2. **Stay grounded**: Only extract terms that appear in or are directly supported by the text. Do not infer beyond what is stated.
3. **Use the evidence quote**: The evidence quote should be a short excerpt from the text that directly supports the extracted term.
4. **One term per extraction**: Each entry in the extracted_terms list should represent one distinct biomedical concept. If the text mentions "hearing loss and intellectual disability", these are two separate entries.
5. **Handle ambiguity with Low confidence**: If a term is implied but not clearly stated, extract it with Low confidence and explain the ambiguity in the evidence quote.
6. **Check for negation before extracting**: Before adding any term, confirm the text is asserting the patient HAS or SHOWS the finding — not that they lack a pathological condition. Do not extract negated pathological states as phenotype terms.

### Allowed Categories

The model must assign one of the following categories to each extracted term:

- `phenotype`: an observable characteristic, clinical sign, physical finding, or measurable biological trait
- `disease`: a named disease entity or clinical diagnosis
- `syndrome`: a named syndrome
- `symptom`: a subjective report of clinical disturbance
- `finding`: an objective clinical observation or test result that indicates a phenotypic state

### Confidence Definitions

- `High`: The text explicitly names or clearly describes the concept. There is no ambiguity.
- `Medium`: The text suggests the concept but the language is not fully explicit. More than one interpretation is possible, but one is clearly more likely.
- `Low`: The concept is implied or inferred but not clearly stated. A human reviewer should confirm before using this term downstream.

### Expected Structured Output

For each row, the model should return:

```json
{
  "extracted_terms": [
    {
      "term": "the extracted term string, cleaned and normalized from the text",
      "category": "one of the allowed categories",
      "evidence_quote": "the short text excerpt that supports this term",
      "confidence": "High, Medium, or Low"
    }
  ],
  "extraction_notes": "optional note about anything unusual, ambiguous, or worth flagging for human review"
}
```

If no phenotype or disease terms are found, return an empty list:

```json
{
  "extracted_terms": [],
  "extraction_notes": "No phenotype or disease terms identified in this text."
}
```

### Human-in-the-Loop Reminder

Extracted terms are candidates, not final classifications. All Low confidence extractions and all normalized matches flagged for review should be confirmed by a human reviewer before being used in downstream analysis.
