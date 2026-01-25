---
title: LLMs for ICD Coding - What 3 Recent Papers Tell Us
date: 2026-01-25 23:30:00 +0800
categories: [Literature Review]
tags: [llm, icd]
description: A critical survey of LLM-based medical coding systems from 2024–2025
---
 
Medical coding—assigning ICD-10 codes to clinical notes—is a $15B+ industry bottleneck. Human coders spend ~30 minutes per case, errors cause billing disputes and patient safety issues, and the 70,000-code label space makes automation notoriously hard. Three recent papers attempt to crack this with LLMs. Here's what they reveal about where the field is heading.
 
---

## MedCodER: A Generative AI Assistant for Medical Coding (arXiv 2409.15368)

**Three-stage LLM pipeline** (GPT-4): extraction → retrieval augmentation → re-ranking. Evaluated on extended ACI-BENCH (184 synthetic clinical notes, 360 ICD-10 codes). Claims SOTA over PLM-ICD and LLM Tree-Search.

---

### Structural Contribution: Interpretability-First Design
   Decomposing the task into explicit diagnosis extraction → evidence grounding → code ranking creates an auditable chain that human coders can verify. This contrasts sharply with end-to-end classifiers (PLM-ICD) that offer no justification. The supporting evidence annotation effort—despite its small scale—creates a template for future datasets.

---

## Code Like Humans: A Multi-Agent Solution for Medical Coding (arXiv 2509.05378)

**Four-agent pipeline** mimicking NHS coding workflow: evidence extractor → index navigator → tabular validator → code reconciler. Evaluated on MDACE (302 real MIMIC-III charts with human-verified evidence spans).

---

### Brilliant Insight: The Alphabetic Index Is the Missing Link
   Human coders don't start from memory—they start from the ICD alphabetic index, a resource mapping clinical terms to codes with synonyms, eponyms, and contextual cues. Prior ML approaches ignored this entirely, treating coding as pure memorization. CLH's index navigator retrieves candidate codes from this authoritative source, enabling true open-set inference over the full 70K codes without retraining.

---

## Toward Reliable Clinical Coding with Language Models: Verification and Lightweight Adaptation (arXiv 2510.07629)

**Verification pipeline**: generate codes → expand via ICD hierarchy (siblings, cousins, 1-hop/2-hop neighbors) → LLM-based selection. New double expert-annotated outpatient dataset (207 ACI-BENCH notes with ICD-10-CM). Tests Claude Haiku/Sonnet, GPT-4o, Llama, PLM-ICD. Claims +16 F1 for fine-tuned Haiku-3 with verification.

---

### Practical Contribution: Verification as Model-Agnostic Post-Processing  
   The expansion → selection pipeline is elegantly simple: take predicted codes, retrieve structurally related candidates (siblings, cousins, neighbors from both tabular and index lists), then ask an LLM to pick the best match given the note. This works across model families (Claude, GPT, Llama, PLM-ICD) and requires no retraining. Notably, presenting *descriptions only* (not codes) yields highest verification accuracy—CoT reasoning *hurts* performance here.

---

## High-Level Summary (My Own Perspective)

| Paper | Architecture | Key Innovation | Data |
|-------|-------------|----------------|------|
| **MedCodER** | Extract → Retrieve → Re-rank | Evidence grounding | ACI-BENCH |
| **CLH** | 4-agent NHS workflow | Alphabetic index retrieval | MDACE |
| **Verification** | Generate → Expand → Verify | Hierarchy-aware refinement | ACI-BENCH | 

### What They Agree On
**Pipeline > End-to-End**: All reject monolithic classifiers; decomposition enables interpretability and modularity.

### The Uncomfortable Truth
**Pipeline Fragility and Error Cascades**: End-to-end differentiability was the revolution of the 2010s because it allowed the model to recover from upstream noise. Reverting to discrete, non-differentiable agent hand-offs reintroduces the brittleness we spent a decade eliminating.

### Where to Go Next
1. **Hybrid Retrieval**  
   - **Sparse**: BM25 over alphabetic index (handles synonyms, eponyms)
   - **Dense**: Contrastive-trained embeddings on (diagnosis text, ICD description) pairs
   - **Structural**: Sibling/cousin/neighbor expansion from tabular list
2. **ICD-Aware Tokenizer**  
   Don't use BPE/SentencePiece. Design tokenizer where each ICD code is a *single token*. Vocabulary = 70K codes + special tokens. Eliminates character-level hallucination (no more "I11.0" vs "I11.9" confusion—they're discrete symbols).
3. **Dataset for Agentic LLM**

---