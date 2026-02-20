# OncoConsensus-15K

A multi-LLM pipeline for generating structured oncology QA datasets for medical AI research.

---

## What It Is

oncogen is an asynchronous dataset generation pipeline that produces structured question-answer pairs across oncology sub-domains. It runs concurrently across multiple free LLM providers, applies intelligent rate limiting, and writes a clean JSONL dataset with full resume support.

The first dataset produced by this pipeline, OncoConsensus-15K, is publicly available on Hugging Face.

---

## How It Works

The pipeline pre-generates a question bank by combining seed clinical questions with twenty distinct reasoning angles — covering perspectives such as randomized trial evidence, biomarker-selected populations, elderly patients, quality of life, resistance mechanisms, and real-world data. Each combination becomes a unique row in the final dataset.

Seventeen async workers run simultaneously, each assigned to a specific LLM provider with its own token bucket rate limiter. When a provider hits a rate limit, the task is returned to the shared queue and picked up by any available worker. When a provider exhausts its daily quota, it exits cleanly and the remaining workers absorb the load.

Results are written by a single async writer coroutine to avoid race conditions. Progress is checkpointed every five hundred rows so the run can be safely interrupted and resumed across sessions.

---

## Oncology Sub-Domains

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

## Dataset Structure

Every generated row contains the following fields:

    id                 Unique row identifier
    domain             Oncology sub-domain name
    domain_key         Snake-case domain key for filtering
    question           Full question with reasoning angle
    response           Structured LLM response (see below)
    provider           Name of the LLM that generated the response
    angle_idx          Index of the reasoning angle applied
    timestamp          UTC timestamp of generation

Each response follows this format:

    ANSWER             Yes / No / Likely Yes / Likely No / Unclear / Context-Dependent
    EVIDENCE           2-3 sentences of clinical trial evidence
    CLINICAL_IMPLICATION    1-2 sentences on practical significance
    LIMITATIONS        1 sentence on caveats or evidence gaps

---

## Providers Supported

    Google AI Studio   Gemma 3 — 1B, 4B, 12B, 27B (14,400 requests/day each)
    OpenRouter         Multiple open-source free-tier models as fallback workers

The pipeline prioritizes higher-capacity providers and falls back gracefully as daily quotas are reached.

---

## Getting Started

Install dependencies:

    pip install aiohttp nest_asyncio pandas pyarrow tqdm

Add your API keys at the top of the script:

    GEMINI_API_KEY     = "your_google_ai_studio_key"
    OPENROUTER_API_KEY = "your_openrouter_key"

Run:

    python oncogen.py

Output files are written to /content/Dataset/ by default:

    oncology_dataset.jsonl
    oncology_dataset.parquet

---

## Resume Support

If the run is interrupted, restart it with the same output path. The pipeline reads completed row indices from the existing JSONL file and skips them automatically. No rows are duplicated.

---

## Disclaimer

This pipeline generates synthetic data using general-purpose language models. The output has not been reviewed or verified by board-certified oncologists or medical experts. Generated responses may contain hallucinated clinical trial names, inaccurate statistics, or outdated medical information.

Data produced by this pipeline must not be used for clinical decision-making. It is intended strictly for AI research, model fine-tuning, and benchmarking purposes.

---

## Dataset on Hugging Face

OncoConsensus-15K — huggingface.co/datasets/Rumiii/OncoConsensus-15K

Apache 2.0 License

---

Built by Rumiii · 2026
