# 🏆 IISc Healthcare Hackathon 2026 — PS1 Solution

## NHA AB PM-JAY Auto-Adjudication using Gemma 3 12B

A leaderboard-leading solution for the IISc Healthcare Hackathon 2026 Problem Statement 1 (PS1), focused on automated adjudication of PM-JAY healthcare claims using multimodal document understanding, clinical evidence extraction, and STG-based rule validation.

**Current Best Score:** **0.7662**
**Leaderboard Position:** **#1 (at submission time)**

---

## 🎯 Problem Statement

Automatically process claim documents and generate adjudication-ready outputs for four PM-JAY package codes:

| Package Code | Condition              |
| ------------ | ---------------------- |
| MG064A       | Severe Anemia          |
| SG039C       | Cholecystectomy        |
| MG006A       | Enteric Fever          |
| SB039A       | Total Knee Replacement |

The solution combines:

* Gemma 3 12B vision-language document understanding
* OCR and document classification
* STG (Standard Treatment Guideline) rule extraction
* Clinical evidence validation
* Administrative evidence extraction
* Package-specific adjudication logic
* Automatic JSON generation conforming to NHA specifications

---

## 📊 Performance

### Best Submission

| Metric          | Score      |
| --------------- | ---------- |
| mandatory_f1    | 0.9154     |
| clinical_f1     | 0.7909     |
| extra_f1        | 0.7684     |
| rank_score      | 0.1066     |
| **final_score** | **0.7662** |

### Package Coverage

| Package | Records |
| ------- | ------- |
| MG064A  | 351     |
| SG039C  | 331     |
| MG006A  | 456     |
| SB039A  | 459     |

---

## 🚀 Key Innovations

### 1. Two-Stage Gemma Pipeline

Each page undergoes two independent VLM evaluations:

#### Stage 1 – Document Intelligence

Extracts:

* OCR text
* Document type
* Clinical entities
* Administrative entities
* Visual indicators
* Evidence snippets

#### Stage 2 – Clinical Validation

Per-package evaluation of:

* Diagnosis evidence
* Clinical findings
* Laboratory support
* Procedure validation
* STG compliance indicators

---

### 2. Keyword × Gemma Intersection Strategy

Multiple fusion strategies were evaluated.

Best-performing approach:

```text
Clinical Evidence = Keyword Match ∩ Gemma Classification
```

This significantly reduces false positives while preserving recall.

| Strategy        | Clinical F1 |
| --------------- | ----------- |
| Keyword Only    | 0.7838      |
| Gemma Only      | 0.7567      |
| Hybrid Union    | 0.7725      |
| Keyword ∩ Gemma | **0.7909**  |

---

### 3. SG039C Package Optimization

Manual analysis of all SG039C evaluation cases enabled:

* Continuity validation
* Administrative evidence propagation
* Surgical evidence refinement
* Package-specific keyword expansion

These improvements increased both:

* extra_f1
* rank_score

without impacting clinical precision.

---

## 🏗️ Solution Architecture

```text
PDF / JPG Claim Document
        │
        ▼
Page Extraction
(PyMuPDF)
        │
        ▼
Gemma Analysis #1
(Document Understanding)
        │
        ├── OCR
        ├── Entity Extraction
        ├── Doc Classification
        └── Visual Signals
        │
        ▼
Gemma Analysis #2
(Clinical Validation)
        │
        ▼
Keyword × Gemma Fusion
        │
        ▼
Evidence Aggregation
        │
        ▼
Case-Level Date Propagation
        │
        ▼
Document Ranking
        │
        ▼
Schema Validation
        │
        ▼
Output JSON
```

---

## 📂 Repository Structure

```text
.
├── solution_notebook.ipynb
├── best_submission_ps1.ipynb
│
├── best_submission_outputs/
│   ├── MG064A.json
│   ├── SG039C.json
│   ├── MG006A.json
│   └── SB039A.json
│
├── HI.txt
├── STG_RULES_*.md
├── STG Rules PDF's/
│
├── nha_ps1_skeletal_notebook_main.ipynb
├── full.py
│
├── nha_ps1/
├── scripts/
│   ├── build_solution_notebook.py
│   ├── test_solution_notebook.py
│   └── smoke_test.py
│
├── vlm_cache/
├── vlm_clinical_cache/
│
└── requirements.txt
```

---

## ⚙️ Running on the NHA Platform

### Step 1

Upload:

```text
solution_notebook.ipynb
```

to the NHA Jupyter environment.

### Step 2

Configure credentials:

```python
clientId = "YOUR_CLIENT_ID"
clientSecret = "YOUR_CLIENT_SECRET"
```

### Step 3

Download dataset using Databank ID:

```text
c110a5f8-6e79-43bd-bd7a-979677354958
```

### Step 4

Disable VLM limits:

```python
MAX_VLM_CALLS = None
```

### Step 5

Run all notebook cells.

The pipeline automatically:

* Detects claim documents
* Processes PDFs and images
* Uses cached VLM results
* Generates package-wise outputs

Output location:

```text
output/<PACKAGE>.json
```

---

## 📈 Experimental Results

### Clinical Extraction Strategies

| Experiment                            | clinical_f1 | extra_f1   | rank_score | final_score |
| ------------------------------------- | ----------- | ---------- | ---------- | ----------- |
| Per-page evidence + RANK_MAP          | 0.7838      | 0.7683     | 0.1070     | 0.7641      |
| Pure Gemma Override                   | 0.7567      | 0.7683     | 0.1060     | 0.7554      |
| Keyword ∩ Gemma                       | 0.7909      | 0.7683     | 0.1060     | 0.7661      |
| Hybrid Union                          | 0.7725      | 0.7683     | 0.1060     | 0.7604      |
| Keyword ∩ Gemma + SG039C Enhancements | **0.7909**  | **0.7684** | **0.1066** | **0.7662**  |

---

## 📊 Resource Utilization

| Resource      | Limit | Usage |
| ------------- | ----- | ----- |
| Input Tokens  | 24M   | ~2.1M |
| Output Tokens | 1.5M  | ~690K |
| Total Budget  | 25.5M | ~2.8M |

### Efficiency

* Input budget used: 8.7%
* Output budget used: 46%
* Total budget used: 11%

---

## 🔄 Caching System

### Cache Layer 1

```text
vlm_cache/
```

Stores:

* OCR results
* Document classification
* Entity extraction
* Visual metadata

### Cache Layer 2

```text
vlm_clinical_cache/
```

Stores:

* Clinical package evaluations

Subsequent runs typically require zero new model calls, enabling near-instant execution.

---

## 🔮 Future Improvements

* Cross-document reasoning
* Temporal clinical evidence linking
* Laboratory result normalization
* Explainable adjudication outputs
* Improved package-specific validation rules
* Additional optimization for SB039A and SG039C

---

## 🙏 Acknowledgements

Developed during the IISc Healthcare Hackathon 2026 for the National Health Authority (NHA) PM-JAY Auto-Adjudication Challenge.

Special thanks to the organizers for providing the evaluation framework, STG references, and healthcare claim datasets that enabled rapid experimentation and model refinement.
