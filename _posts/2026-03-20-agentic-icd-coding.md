---
title: Building an Agentic ICD-10 Coding Skill
date: 2026-03-20 23:00:00 +0800
categories: [Project]
tags: [llm, icd, agent, skills]
description: How we turned an LLM into a traceable, guideline-compliant medical coding assistant using tool use, official CMS data, and the same 10-step workflow that certified coders follow.
---


## The Problem: Medical Coding Is Hard, and AI Keeps Getting It Wrong

Medical coding — the translation of clinical documentation into standardized ICD-10-CM codes — is one of healthcare's most consequential bottlenecks. Every diagnosis on every patient encounter must be mapped to the correct code from a set of over **74,000 billable codes**, following hundreds of pages of official guidelines that change annually.

The stakes are high:

- **35% of claim denials** are related to coding errors ([Change Healthcare, 2022](https://www.codeemr.com/icd-10-codes-to-reduce-denials-and-improve-reimbursement/))
- **15–20% of claims** are initially denied, many due to specificity or sequencing mistakes
- A single missed 7th character on a fracture code can trigger a denial
- Incorrect sequencing of sepsis codes can misrepresent severity and affect reimbursement

Large Language Models seemed like a natural fit — they're great at reading clinical narratives. But early attempts at AI-assisted coding have revealed a fundamental problem: **LLMs hallucinate codes**. They generate plausible-looking ICD-10 codes from training data, but those codes may be outdated, non-billable, or flat-out wrong. As noted in recent research from [JMIR Formative Research (2025)](https://formative.jmir.org/2025/1/e60095), AI automation for ICD coding "still underperforms compared to human accuracy and lacks the explainability needed" for clinical adoption.

The missing ingredient isn't a better model — it's **a better workflow**.

---

## The Agentic Approach: Tools, Not Just Tokens

Recent research has converged on a powerful insight: the best results come not from asking an LLM to predict codes end-to-end, but from giving it **tools** and letting it **follow the same process human coders use**. A [December 2025 preprint](https://www.preprints.org/manuscript/202512.2138) comparing three automation patterns — standalone deep learning (PLM-ICD), LLM-only generation, and an agentic LLM pipeline — found that the agentic approach, where the LLM orchestrates searches and lookups through external tools, produced the most accurate and explainable results.

This is the philosophy behind the **ICD-10-CM Auto-Coding Skill** — a modular skill for Agentic tools that transforms an LLM into a medical coding agent with:

1. **Real tools** that search official CMS data (not training data)
2. **A structured 10-step workflow** that mirrors certified coder practice
3. **Built-in validation** that catches errors before they leave the system
4. **Full traceability** — every code assignment links back to Index → Tabular → Guidelines

---

## Architecture: How It Works

The skill is not a black-box classifier. It's a **workflow orchestrator** that gives the LLM four Python tools and a set of expert-written reference documents:

```
Clinical Note
     │
     ▼
┌──────────────────────┐
│  Extract Conditions   │  LLM reads the note, identifies diagnoses,
│                       │  laterality, acuity, causal relationships
└──────────┬───────────┘
           ▼
┌──────────────────────┐
│  search_index.py      │  Searches the official Alphabetic Index (XML)
│                       │  by main term + subterms
└──────────┬───────────┘
           ▼
┌──────────────────────┐
│  lookup_tabular.py    │  Verifies code in the Tabular List — checks
│                       │  Excludes1/2, Code First, 7th characters,
│                       │  combination codes, specificity
└──────────┬───────────┘
           ▼
┌──────────────────────┐
│  Apply Guidelines     │  LLM applies sequencing rules, chapter-specific
│                       │  guidance (sepsis, diabetes, injuries, SDOH...)
└──────────┬───────────┘
           ▼
┌──────────────────────┐
│  validate_code.py     │  Validates structure, billability against
│                       │  the 74K FY2025 code set, Excludes1 conflicts
└──────────┬───────────┘
           ▼
┌──────────────────────┐
│  Structured Output    │  Codes + descriptions + rationale +
│                       │  guideline references
└──────────────────────┘
```

### The Key Insight: Tool-Grounded Coding Eliminates Hallucination

When the LLM encounters "Type 2 diabetes with chronic kidney disease stage 4," it doesn't guess `E11.22` from memory. Instead, it:

1. **Searches the index**: `search_index.py "Diabetes" "type 2" "kidney"` → finds E11.22 under "Diabetes, type 2, with, kidney complication"
2. **Verifies in tabular**: `lookup_tabular.py "E11.22"` → confirms it's valid, sees "Use additional code to identify stage of CKD (N18.-)"
3. **Looks up the CKD stage**: `lookup_tabular.py "N18.4"` → confirms N18.4 = CKD stage 4
4. **Validates**: `validate_code.py "E11.22" "N18.4" --check-excludes` → both billable, no conflicts

Every code is **looked up from official source data**, not recalled from training. The LLM's role is understanding the clinical narrative and navigating the coding workflow — not memorizing codes.

---

## What Makes This Different from Other AI Coding Tools

| Approach | How It Works | Weakness |
|----------|-------------|----------|
| **Classification models** (PLM-ICD, etc.) | Trained on labeled discharge summaries → predicts code set | Fixed code vocabulary; no explanation; misses rare codes |
| **LLM-only** (GPT/Claude generating codes directly) | Prompt with note → model outputs codes | Hallucination; outdated codes; no validation; no traceability |
| **RAG-based** | Retrieves similar past cases → suggests codes | Biased toward training distribution; misses guideline nuances |
| **This agentic skill** | LLM follows structured workflow with tool calls to official data | Requires agent runtime; slower than classification; depends on LLM's clinical reasoning |

The agentic approach trades speed for **accuracy, explainability, and auditability** — exactly what healthcare compliance demands.

---

## Deep Dive: Handling Complex Scenarios

### Poisoning vs. Adverse Effect vs. Underdosing

One of the trickiest areas in ICD-10-CM coding is drug-related events. The skill includes a decision tree that the LLM follows:

- **Adverse effect**: Drug taken correctly → code the manifestation FIRST, then the T code
- **Poisoning**: Overdose, wrong drug, or recreational use → T code FIRST, then manifestation
- **Underdosing**: Less drug taken than prescribed → T code with underdosing character, plus noncompliance Z code

**Example — Adverse effect of warfarin causing GI hemorrhage:**
```
K92.2    GI hemorrhage (manifestation — sequenced FIRST)
T45.515A Adverse effect of anticoagulants, initial encounter (SECOND)
```

The sequencing is counterintuitive (manifestation before cause), which is exactly why coders — and AI — get it wrong. The skill's reference documents make this explicit with worked examples.

### Sepsis Coding: A Three-Layer Sequence

Sepsis coding requires strict layering:

```
1. A41.02   Sepsis due to MRSA                    (systemic infection — principal)
2. R65.20   Severe sepsis without septic shock     (only if organ dysfunction present)
3. N17.9    Acute kidney failure                   (the organ dysfunction)
```

Missing R65.20 when organ dysfunction is present = undercoding. Including it without organ dysfunction = overcoding. The skill's guidelines reference walks the LLM through the decision tree step by step.

### The "With" Convention

ICD-10-CM presumes a causal relationship between conditions linked by "with" in the index. This means:

> "Type 2 diabetes" + "chronic kidney disease" in the same patient → code as **E11.22** (diabetes with CKD), even if the provider doesn't explicitly state the CKD is caused by diabetes.

This convention is one of the most commonly missed rules in manual coding. The skill highlights it in the guidelines, worked examples, and common pitfalls section.

---

## The Skill Stack

The skill is organized as a self-contained directory that an agent can load:

```
icd10cm-codes/
├── SKILL.md                    # Master skill definition (triggers, workflow, rules)
├── scripts/
│   ├── search_index.py         # Alphabetic Index search (XML parser)
│   ├── search_codes.py         # Keyword search across 74K codes
│   ├── lookup_tabular.py       # Tabular List lookup with full hierarchy
│   └── validate_code.py        # Code validation + Excludes1 conflict check
└── references/
    ├── data/                   # Official CMS FY2025 XML + text files
    ├── guidelines-key-rules.md # Condensed guidelines for all 21 chapters
    ├── coding-workflow.md      # 10-step process + 12 worked examples
    └── chapter-overview.md     # Quick reference of all chapters + code ranges
```

**Zero external dependencies.** The scripts use only Python's standard library (`xml.etree.ElementTree`, `argparse`, `re`). The data files are the official CMS releases. The reference documents are expert-written summaries of the 196-page official guidelines.

---

## Results: What the Agent Produces

For every coding request, the agent outputs a structured report:

```markdown
## ICD-10-CM Coding Results

**Encounter type**: Inpatient
**Coding date**: 2025-03-13

### Principal Diagnosis
| Code    | Description                          | Rationale                        |
|---------|--------------------------------------|----------------------------------|
| A41.02  | Sepsis due to MRSA                   | Systemic infection — principal    |

### Secondary Diagnoses
| # | Code   | Description                          | Rationale                        |
|---|--------|--------------------------------------|----------------------------------|
| 2 | R65.20 | Severe sepsis without septic shock   | Organ dysfunction present (AKI)  |
| 3 | N17.9  | Acute kidney failure, unspecified    | Organ dysfunction                |

### Coding Notes
- Sequencing per Guideline I.C.1.d: systemic infection → R65.2x → organ dysfunction
- No septic shock documented → R65.20 (not R65.21)
- All codes validated against FY2025 code set ✓
```

Every code has a **rationale**. Every sequencing decision references a **guideline section**. This isn't just coding — it's coding with an audit trail.

---

## Lessons Learned: Building Domain Skills for LLMs

### 1. Give the LLM tools, not just knowledge

Putting all 74K codes in a prompt doesn't work. Giving the LLM a `search_index.py` script and teaching it when to call it does. The skill definition (`SKILL.md`) acts as a domain-specific system prompt that shapes the agent's behavior.

### 2. Worked examples are the best teacher

The 12 worked examples in `coding-workflow.md` — covering diabetes, sepsis, poisoning, adverse effects, burns, pressure ulcers, SDOH — are more effective than pages of abstract guidelines. The LLM learns the *pattern* of correct coding from concrete demonstrations.

### 3. Validation must be a separate, mandatory step

The `validate_code.py` script catches errors that the LLM's reasoning misses: incomplete codes, missing 7th characters, invalid codes, and Excludes1 conflicts. Making validation a distinct step in the workflow — not an afterthought — dramatically reduces output errors.

### 4. Condense, don't dump

The official ICD-10-CM guidelines are 196 pages. The condensed `guidelines-key-rules.md` is ~20KB. The art is in selecting the rules that matter most and presenting them in a format the LLM can efficiently reference. Chapter-specific rules, decision trees, and tables work far better than prose.

### 5. Pin your data to a fiscal year

ICD-10-CM codes change annually. The skill is explicitly pinned to **FY2025** (effective October 1, 2024). When FY2026 drops, you update four data files and review the guidelines — the workflow and tools remain the same.

---

## The Bigger Picture: Agentic AI in Healthcare

This skill is one example of a broader pattern emerging in healthcare AI: **agentic systems that combine LLM reasoning with domain-specific tools and data**. Instead of asking "Can GPT-4 pass a medical coding exam?", the question becomes "Can an LLM follow the same workflow a certified coder uses, with the same reference materials, and produce traceable results?"

The answer, increasingly, is yes — when the system is designed correctly.

The [CLH (Code Like Humans) framework](https://arxiv.org/html/2509.05378v1) from recent research takes a similar approach: a multi-agent system where LLMs interact with tools, data sources, and other agents in a goal-oriented manner. The [AWS generative AI medical coding pipeline](https://aws.amazon.com/blogs/industries/generative-ai-enabled-medical-coding-on-aws/) follows the same philosophy at enterprise scale.

What's unique about the skill-based approach is its **portability and transparency**. It's a directory of Python scripts, markdown files, and official data — no cloud infrastructure, no fine-tuning, no proprietary models. Any LLM agent runtime that supports tool use can load it.

---

## Try It Yourself

The skill is available as an [open source repository](https://github.com/EricFu1120/icd10cm-codes-skill) that can be integrated into any LLM agent runtime supporting tool/skill invocation.

The official CMS FY2025 data files can be downloaded from [cms.gov](https://www.cms.gov/medicare/coding-billing/icd-10-codes/2025-icd-10-cm).

---

## What's Next

- **ICD-10-PCS (Procedure Coding)** skill using the same agentic pattern
- **CPT/HCPCS** integration for complete encounter coding
- **DRG assignment** logic layered on top of diagnosis codes
- **Audit mode** — given existing codes, verify correctness and suggest improvements
- **Multi-year support** — load FY2024 and FY2025 simultaneously for conversion workflows

---

*The ICD-10-CM code set is a public domain work product of CMS/NCHS. This skill is provided for educational and research purposes. All code assignments should be reviewed by certified coding professionals before use in production billing systems.*

---

