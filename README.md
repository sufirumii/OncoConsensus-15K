# OncoConsensus-15K

A structured oncology question-answer dataset covering 12 cancer sub-domains, designed for medical LLM fine-tuning, benchmarking, and clinical reasoning research.

---

## Overview

OncoConsensus-15K contains 15,000 question-answer pairs spanning the major areas of clinical oncology. Each entry presents a research-grade clinical question paired with a structured response covering evidence, clinical significance, and known limitations.

The dataset is available on Hugging Face under the Apache 2.0 license and is free to use for research and non-commercial purposes.

---

## Screenshots

<img width="1920" height="1112" alt="1" src="https://github.com/user-attachments/assets/0ed86fce-a545-4293-ad81-3a2126983d0a" />


<img width="1920" height="1120" alt="2" src="https://github.com/user-attachments/assets/e6380e95-de22-4548-b256-6c24ef33d94d" />


<img width="1916" height="1093" alt="3" src="https://github.com/user-attachments/assets/82249568-a881-4ef2-8143-2806b705c295" />


---

## Sub-Domains

- Breast Cancer
- Lung Cancer
- Colorectal Cancer
- Hematologic Malignancies
- Immunotherapy and Checkpoint Inhibitors
- Targeted Therapy and Biomarkers
- Tumor Biology and Microenvironment
- Radiation and Surgical Oncology
- Pediatric Oncology
- Rare Cancers and Sarcomas
- Gynecologic Oncology
- Prostate and Pancreatic Cancer

---

## Structure

Every row in the dataset contains the following fields:

    id                      Unique row identifier
    domain                  Oncology sub-domain name
    domain_key              Snake-case domain key for filtering
    question                Clinical question with reasoning angle
    response                Structured four-field response
    provider                LLM model that generated the response
    angle_idx               Reasoning angle index
    timestamp               UTC timestamp of generation

Each response is structured into four consistent fields:

    ANSWER                  Yes / No / Likely Yes / Likely No / Unclear / Context-Dependent
    EVIDENCE                2-3 sentences of clinical trial evidence
    CLINICAL_IMPLICATION    1-2 sentences on practical clinical significance
    LIMITATIONS             1 sentence on caveats or evidence gaps

---

## Sample Row

    {
      "id": "onco_0000042",
      "domain": "Lung Cancer",
      "domain_key": "lung_cancer",
      "question": "Does EGFR mutation predict response to TKIs in NSCLC?",
      "response": "ANSWER: Yes\nEVIDENCE: Multiple phase III trials including FLAURA demonstrated
                   significantly improved progression-free survival with EGFR TKIs versus
                   chemotherapy in EGFR-mutant NSCLC...\nCLINICAL_IMPLICATION: EGFR mutation
                   testing is now standard practice before initiating first-line therapy...\n
                   LIMITATIONS: Acquired resistance via T790M and other mechanisms limits
                   long-term durability of response.",
      "provider": "Gemma3-27B-G",
      "angle_idx": 1,
      "timestamp": "2026-02-19T22:25:16Z"
    }

---

## Loading the Dataset

    from datasets import load_dataset

    ds = load_dataset("Rumiii/OncoConsensus-15K")

Filter by sub-domain:

    lung = ds["train"].filter(lambda x: x["domain_key"] == "lung_cancer")

Format for fine-tuning:

    def format(example):
        return {
            "text": f"### Question\n{example['question']}\n\n### Answer\n{example['response']}"
        }

    ds_formatted = ds["train"].map(format)

---

## Intended Use

- Fine-tuning language models on structured oncology QA
- Benchmarking clinical reasoning capabilities of LLMs
- Building retrieval-augmented generation knowledge bases for oncology
- Evaluating hallucination rates in medical language models

---

## Disclaimer

This dataset is synthetically generated using large language models and has not been reviewed or verified by board-certified oncologists or medical experts. Individual entries may contain hallucinated clinical trial names, inaccurate statistics, or outdated medical information.

This dataset must not be used for clinical decision-making. It is intended strictly for AI research, model fine-tuning, and benchmarking purposes. Always consult peer-reviewed literature and qualified medical professionals for clinical guidance.

---

## License

Apache 2.0

---

## Dataset on Hugging Face

huggingface.co/datasets/Rumiii/OncoConsensus-15K

---

Built by Rumiii · 2026
