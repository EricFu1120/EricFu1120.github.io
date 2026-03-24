---
title: What We Can Learn from Anthropic to Build Our Own Agent
date: 2026-03-24 21:45:00 +0800
categories: [Learning]
tags: [llm, agent, context-engineering, evaluation]
description: A deep-dive on Anthropic engineering blog posts, distilling every actionable lesson into a practical guide for building production-grade AI agents.
---

## Table of Contents

1. [Introduction](#introduction)
2. [The Six Pillars](#the-six-pillars)
3. [Lesson 1: Start Simple](#lesson-1-start-simple--the-complexity-spectrum)
4. [Lesson 2: Master the Five Workflow Patterns](#lesson-2-master-the-five-workflow-patterns)
5. [Lesson 3: Design Your Agent-Computer Interface](#lesson-3-design-your-agent-computer-interface-aci)
6. [Lesson 4: Context Engineering](#lesson-4-context-engineering--the-new-frontier)
7. [Lesson 5: Build Effective Tools](#lesson-5-build-effective-tool-ecosystems)
8. [Lesson 6: Harness Long-Running Agents](#lesson-6-harness-long-running-agents)
9. [Lesson 7: Scale with Multi-Agent Architectures](#lesson-7-scale-with-multi-agent-architectures)
10. [Lesson 8: Evaluation-Driven Development](#lesson-8-evaluation-driven-development)
11. [The Agent Builder's Checklist](#the-agent-builders-checklist)
12. [Conclusion](#conclusion)

---

## Introduction

Anthropic has published a series of deeply practical engineering blog posts that, taken together, form arguably the most comprehensive public guide to building AI agents today. These aren't theoretical musings — they are battle-tested lessons from teams that built Claude Code, the Claude Research feature, and worked with dozens of enterprise customers deploying agents in production.

This blog synthesizes **all six articles** into a single, actionable guide for building your own agent.

---

## The Six Pillars

| # | Article | Core Theme | Key Takeaway |
|---|---------|-----------|--------------|
| 1 | **Building Effective AI Agents** | Architecture patterns | Start simple; use composable patterns instead of complex frameworks |
| 2 | **Effective Context Engineering** | Context management | Context is a finite resource — engineer it like a precious budget |
| 3 | **Writing Effective Tools** | Tool design | Tools are contracts between deterministic systems and non-deterministic agents |
| 4 | **Effective Harnesses for Long-Running Agents** | Multi-session persistence | Use initializer + coding agent pattern with structured state handoff |
| 5 | **How We Built Our Multi-Agent Research System** | Multi-agent coordination | Multi-agent systems excel at parallelizable, high-value tasks |
| 6 | **Demystifying Evals for AI Agents** | Evaluation methodology | Eval-driven development is non-negotiable for production agents |

<div align="center">

```svg
<svg viewBox="0 0 900 320" xmlns="http://www.w3.org/2000/svg" font-family="Segoe UI, Arial, sans-serif">
  <defs>
    <linearGradient id="g1" x1="0%" y1="0%" x2="100%" y2="100%">
      <stop offset="0%" style="stop-color:#6366f1;stop-opacity:1"/>
      <stop offset="100%" style="stop-color:#8b5cf6;stop-opacity:1"/>
    </linearGradient>
    <linearGradient id="g2" x1="0%" y1="0%" x2="100%" y2="100%">
      <stop offset="0%" style="stop-color:#ec4899;stop-opacity:1"/>
      <stop offset="100%" style="stop-color:#f43f5e;stop-opacity:1"/>
    </linearGradient>
    <linearGradient id="g3" x1="0%" y1="0%" x2="100%" y2="100%">
      <stop offset="0%" style="stop-color:#14b8a6;stop-opacity:1"/>
      <stop offset="100%" style="stop-color:#06b6d4;stop-opacity:1"/>
    </linearGradient>
    <filter id="shadow" x="-5%" y="-5%" width="115%" height="115%">
      <feDropShadow dx="0" dy="3" stdDeviation="4" flood-color="#00000022"/>
    </filter>
  </defs>
  <text x="450" y="35" text-anchor="middle" font-size="18" font-weight="bold" fill="#1e293b">The Six Pillars of Anthropic's Agent Engineering Knowledge</text>
  <g filter="url(#shadow)">
    <rect x="20" y="60" width="270" height="100" rx="12" fill="url(#g1)"/>
    <text x="155" y="100" text-anchor="middle" fill="white" font-size="13" font-weight="bold">1. Building Effective</text>
    <text x="155" y="120" text-anchor="middle" fill="white" font-size="13" font-weight="bold">AI Agents</text>
    <text x="155" y="145" text-anchor="middle" fill="#e0e7ff" font-size="10">Patterns &amp; Architecture</text>
  </g>
  <g filter="url(#shadow)">
    <rect x="315" y="60" width="270" height="100" rx="12" fill="url(#g2)"/>
    <text x="450" y="100" text-anchor="middle" fill="white" font-size="13" font-weight="bold">2. Context Engineering</text>
    <text x="450" y="120" text-anchor="middle" fill="white" font-size="13" font-weight="bold">for AI Agents</text>
    <text x="450" y="145" text-anchor="middle" fill="#fce7f3" font-size="10">Managing the Attention Budget</text>
  </g>
  <g filter="url(#shadow)">
    <rect x="610" y="60" width="270" height="100" rx="12" fill="url(#g3)"/>
    <text x="745" y="100" text-anchor="middle" fill="white" font-size="13" font-weight="bold">3. Writing Effective Tools</text>
    <text x="745" y="120" text-anchor="middle" fill="white" font-size="13" font-weight="bold">for AI Agents</text>
    <text x="745" y="145" text-anchor="middle" fill="#ccfbf1" font-size="10">Tool Design &amp; Optimization</text>
  </g>
  <g filter="url(#shadow)">
    <rect x="20" y="190" width="270" height="100" rx="12" fill="url(#g3)"/>
    <text x="155" y="230" text-anchor="middle" fill="white" font-size="13" font-weight="bold">4. Harnesses for</text>
    <text x="155" y="250" text-anchor="middle" fill="white" font-size="13" font-weight="bold">Long-Running Agents</text>
    <text x="155" y="275" text-anchor="middle" fill="#ccfbf1" font-size="10">Multi-Session Persistence</text>
  </g>
  <g filter="url(#shadow)">
    <rect x="315" y="190" width="270" height="100" rx="12" fill="url(#g1)"/>
    <text x="450" y="230" text-anchor="middle" fill="white" font-size="13" font-weight="bold">5. Multi-Agent</text>
    <text x="450" y="250" text-anchor="middle" fill="white" font-size="13" font-weight="bold">Research System</text>
    <text x="450" y="275" text-anchor="middle" fill="#e0e7ff" font-size="10">Orchestrator-Worker at Scale</text>
  </g>
  <g filter="url(#shadow)">
    <rect x="610" y="190" width="270" height="100" rx="12" fill="url(#g2)"/>
    <text x="745" y="230" text-anchor="middle" fill="white" font-size="13" font-weight="bold">6. Demystifying Evals</text>
    <text x="745" y="250" text-anchor="middle" fill="white" font-size="13" font-weight="bold">for AI Agents</text>
    <text x="745" y="275" text-anchor="middle" fill="#fce7f3" font-size="10">Measurement &amp; Quality</text>
  </g>
</svg>
```

</div>

---

## Lesson 1: Start Simple — The Complexity Spectrum

Anthropic's single most repeated piece of advice across all six articles:

> *"Find the simplest solution possible, and only increase complexity when needed."*

This isn't just philosophy — it's a practical heuristic. The most successful agent implementations they've seen **don't use complex frameworks**. They use simple, composable patterns built directly on LLM APIs.

### The Complexity Decision Framework

1. **Can a single optimized LLM call with retrieval and in-context examples solve this?** If yes, stop here.
2. **Is the task well-defined with predictable subtasks?** Use a workflow (predefined code paths).
3. **Does the task require flexibility and model-driven decisions?** Use an agent (LLM controls the flow).

<div align="center">

<svg viewBox="0 0 860 380" xmlns="http://www.w3.org/2000/svg" font-family="Segoe UI, Arial, sans-serif">
  <defs>
    <linearGradient id="cg1" x1="0%" y1="0%" x2="100%" y2="0%">
      <stop offset="0%" style="stop-color:#22c55e"/>
      <stop offset="50%" style="stop-color:#eab308"/>
      <stop offset="100%" style="stop-color:#ef4444"/>
    </linearGradient>
    <marker id="arrowhead" markerWidth="10" markerHeight="7" refX="9" refY="3.5" orient="auto">
      <polygon points="0 0, 10 3.5, 0 7" fill="#64748b"/>
    </marker>
  </defs>
  <text x="430" y="30" text-anchor="middle" font-size="17" font-weight="bold" fill="#1e293b">The Complexity Spectrum: When to Use What</text>
  <rect x="80" y="50" width="700" height="16" rx="8" fill="url(#cg1)" opacity="0.7"/>
  <text x="80" y="82" font-size="11" fill="#16a34a" font-weight="bold">Low Complexity</text>
  <text x="700" y="82" font-size="11" fill="#dc2626" font-weight="bold" text-anchor="end">High Complexity</text>
  <line x1="80" y1="95" x2="780" y2="95" stroke="#64748b" stroke-width="2" marker-end="url(#arrowhead)"/>
  <rect x="40" y="110" width="175" height="120" rx="10" fill="#f0fdf4" stroke="#22c55e" stroke-width="2"/>
  <text x="128" y="135" text-anchor="middle" font-size="12" font-weight="bold" fill="#166534">Single LLM Call</text>
  <text x="128" y="155" text-anchor="middle" font-size="10" fill="#374151">+ retrieval + examples</text>
  <line x1="50" y1="168" x2="205" y2="168" stroke="#86efac" stroke-width="1"/>
  <text x="128" y="186" text-anchor="middle" font-size="9" fill="#6b7280">Classification</text>
  <text x="128" y="200" text-anchor="middle" font-size="9" fill="#6b7280">Summarization, Q&amp;A</text>
  <rect x="235" y="110" width="190" height="120" rx="10" fill="#fefce8" stroke="#eab308" stroke-width="2"/>
  <text x="330" y="135" text-anchor="middle" font-size="12" font-weight="bold" fill="#854d0e">Workflows</text>
  <text x="330" y="155" text-anchor="middle" font-size="10" fill="#374151">Predefined code paths</text>
  <line x1="245" y1="168" x2="415" y2="168" stroke="#fde047" stroke-width="1"/>
  <text x="330" y="186" text-anchor="middle" font-size="9" fill="#6b7280">Prompt chaining, Routing</text>
  <text x="330" y="200" text-anchor="middle" font-size="9" fill="#6b7280">Parallelization</text>
  <rect x="445" y="110" width="175" height="120" rx="10" fill="#fff7ed" stroke="#f97316" stroke-width="2"/>
  <text x="533" y="135" text-anchor="middle" font-size="12" font-weight="bold" fill="#9a3412">Single Agent</text>
  <text x="533" y="155" text-anchor="middle" font-size="10" fill="#374151">LLM directs its process</text>
  <line x1="455" y1="168" x2="610" y2="168" stroke="#fdba74" stroke-width="1"/>
  <text x="533" y="186" text-anchor="middle" font-size="9" fill="#6b7280">Coding agents</text>
  <text x="533" y="200" text-anchor="middle" font-size="9" fill="#6b7280">Support, Computer use</text>
  <rect x="640" y="110" width="185" height="120" rx="10" fill="#fef2f2" stroke="#ef4444" stroke-width="2"/>
  <text x="733" y="135" text-anchor="middle" font-size="12" font-weight="bold" fill="#991b1b">Multi-Agent</text>
  <text x="733" y="155" text-anchor="middle" font-size="10" fill="#374151">Separate context windows</text>
  <line x1="650" y1="168" x2="815" y2="168" stroke="#fca5a5" stroke-width="1"/>
  <text x="733" y="186" text-anchor="middle" font-size="9" fill="#6b7280">Deep research</text>
  <text x="733" y="200" text-anchor="middle" font-size="9" fill="#6b7280">Parallel exploration</text>
  <text x="430" y="265" text-anchor="middle" font-size="13" font-weight="bold" fill="#475569">Trade-offs as complexity increases:</text>
  <text x="430" y="290" text-anchor="middle" font-size="11" fill="#dc2626">Higher: Latency | Cost | Error Compounding | Debugging Difficulty</text>
  <text x="430" y="310" text-anchor="middle" font-size="11" fill="#16a34a">Higher: Flexibility | Task Scope | Autonomy | Parallel Capacity</text>
  <rect x="150" y="330" width="560" height="36" rx="8" fill="#eff6ff" stroke="#3b82f6" stroke-width="1.5"/>
  <text x="430" y="353" text-anchor="middle" font-size="12" fill="#1e40af" font-weight="bold">Anthropic's Rule: Add complexity ONLY when it demonstrably improves outcomes</text>
</svg>

</div>

### Agents vs Workflows — A Critical Distinction

- **Workflows**: LLMs and tools orchestrated through **predefined code paths**. The developer controls the flow.
- **Agents**: LLMs **dynamically direct their own processes** and tool usage. The model controls the flow.

Both are "agentic systems," but knowing which you need prevents over-engineering.

---

## Lesson 2: Master the Five Workflow Patterns

Anthropic identifies five foundational workflow patterns that cover the vast majority of production use cases. These are building blocks you can combine and customize.

### 1. Prompt Chaining

Sequential steps where each LLM call processes the output of the previous one. Add programmatic **"gates"** between steps for quality checks.

```
Input → [LLM Step 1] → Gate (validate) → [LLM Step 2] → Gate → [LLM Step 3] → Output
```

**When to use:** Tasks decomposable into fixed sequential steps.
**Example:** Generate marketing copy → check tone → translate to another language.

### 2. Routing

Classify the input, then direct it to a specialized handler. This lets you route easy queries to fast/cheap models and hard queries to powerful ones.

```
Input → [Classifier LLM] → Route A: Handler for refunds
                          → Route B: Handler for technical issues
                          → Route C: Handler for general inquiries
```

**When to use:** Distinct input categories needing different handling.
**Example:** Customer support triage, model selection based on complexity.

### 3. Parallelization

Two variations:
- **Sectioning:** Break task into independent subtasks, run in parallel, merge results.
- **Voting:** Run same task multiple times for diverse perspectives, aggregate.

**When to use:** Independent subtasks or need for multiple perspectives.
**Example:** Code review where one LLM writes code and another screens it for vulnerabilities. Content moderation with multiple guardrail checks.

### 4. Orchestrator-Workers

A central LLM dynamically breaks down tasks, delegates to worker LLMs, and synthesizes results. Unlike parallelization, subtasks are **NOT predefined** — they're determined at runtime by the orchestrator.

**When to use:** Complex tasks where subtasks aren't predictable upfront.
**Example:** Multi-file code changes, multi-source research tasks.

### 5. Evaluator-Optimizer

One LLM generates a response → another evaluates and provides feedback → the generator refines. This loop continues until quality criteria are met.

```
[Generator LLM] → output → [Evaluator LLM] → feedback → [Generator LLM] → improved output → ...
```

**When to use:** Tasks with clear evaluation criteria where iterative refinement adds measurable value.
**Example:** Literary translation, complex search refinement, code optimization.

### Pattern Selection Guide

| Pattern | Best For | Example |
|---------|----------|---------|
| **Prompt Chaining** | Fixed sequential steps | Generate → translate → format |
| **Routing** | Distinct input categories | Support triage |
| **Parallelization** | Independent subtasks / voting | Guardrails, code review |
| **Orchestrator-Workers** | Unpredictable subtasks | Multi-file refactoring |
| **Evaluator-Optimizer** | Clear quality criteria + iterative refinement | Translation, search |

### The Autonomous Agent Loop

Beyond workflows, a true **autonomous agent** is simply an LLM using tools based on environmental feedback in a loop:

```
while not done:
    action = llm.decide(context, tools)
    result = execute(action)
    context.update(result)
    if llm.should_stop(context):
        done = True
```

Three core principles for autonomous agents:
1. **Maintain simplicity** in the agent loop design
2. **Prioritize transparency** — show the agent's planning steps to the user
3. **Carefully craft the ACI** with thorough tool documentation and testing

---

## Lesson 3: Design Your Agent-Computer Interface (ACI)

One of Anthropic's most original insights is the **Agent-Computer Interface (ACI)** — the analog of HCI (Human-Computer Interface), but for AI agents. Anthropic reports spending **more time optimizing tools than the overall prompt** when building their SWE-bench agent.

### ACI Design Principles

1. **Put yourself in the model's shoes.** If tool usage isn't obvious from its description and parameters, it won't be obvious to the model. Good tool definitions include example usage, edge cases, input format requirements, and clear boundaries.

2. **Think like writing a great docstring for a junior developer.** Especially important when using many similar tools — parameter names and descriptions must disambiguate.

3. **Test empirically.** Run many example inputs to see what mistakes the model makes, then iterate.

4. **Poka-yoke your tools (error-proof them).** Change arguments so mistakes become harder. Example: Anthropic changed their file edit tool to require **absolute file paths** after seeing errors with relative paths.

5. **Choose formats the model can write easily:**
   - Don't require diffs (the model must predict chunk headers before writing code)
   - Don't require JSON-escaped code (extra escaping of newlines and quotes)
   - Keep formats close to what appears naturally in training data

### Tool Format Decision Guide

| Format Choice | Why It Matters |
|--------------|---------------|
| Absolute paths > relative paths | Eliminates state-dependent errors |
| Markdown code blocks > JSON-wrapped code | No escaping overhead |
| Full file rewrites > diffs | No need to count lines in advance |
| Provide "thinking" tokens before committal output | Prevents painting into corners |

---

## Lesson 4: Context Engineering — The New Frontier

Anthropic argues that we're moving beyond "prompt engineering" to **context engineering** — the art of curating the optimal set of tokens at every inference step.

### Why Context Is a Finite Resource

LLMs have limited working memory. Anthropic identifies a phenomenon called **context rot**: as token count increases, the model's ability to accurately recall information **decreases**. This stems from the transformer's n² pairwise attention relationships — as context grows, attention gets stretched thin.

<div align="center">

```svg
<svg viewBox="0 0 800 300" xmlns="http://www.w3.org/2000/svg" font-family="Segoe UI, Arial, sans-serif">
  <text x="400" y="30" text-anchor="middle" font-size="17" font-weight="bold" fill="#1e293b">From Prompt Engineering to Context Engineering</text>
  <rect x="30" y="50" width="340" height="130" rx="10" fill="#fef2f2" stroke="#fca5a5" stroke-width="1.5"/>
  <text x="200" y="75" text-anchor="middle" font-size="13" font-weight="bold" fill="#991b1b">Prompt Engineering (Old)</text>
  <text x="50" y="98" font-size="10" fill="#374151">Focus: Writing the perfect system prompt</text>
  <text x="50" y="115" font-size="10" fill="#374151">Scope: Static, one-shot tasks</text>
  <text x="50" y="132" font-size="10" fill="#374151">Approach: Craft the right words and phrases</text>
  <text x="50" y="149" font-size="10" fill="#374151">Context: Mostly just the prompt itself</text>
  <text x="50" y="170" font-size="10" fill="#6b7280" font-style="italic">Best for: Classification, summarization, Q&amp;A</text>
  <rect x="420" y="50" width="360" height="130" rx="10" fill="#f0fdf4" stroke="#86efac" stroke-width="1.5"/>
  <text x="600" y="75" text-anchor="middle" font-size="13" font-weight="bold" fill="#166534">Context Engineering (New)</text>
  <text x="440" y="98" font-size="10" fill="#374151">Focus: Curating ALL tokens at each inference step</text>
  <text x="440" y="115" font-size="10" fill="#374151">Scope: Multi-turn, long-horizon agentic tasks</text>
  <text x="440" y="132" font-size="10" fill="#374151">Approach: Optimize the entire context state</text>
  <text x="440" y="149" font-size="10" fill="#374151">Context: System prompt + tools + history + data</text>
  <text x="440" y="170" font-size="10" fill="#6b7280" font-style="italic">Best for: Agents running in loops over many turns</text>
  <line x1="370" y1="115" x2="420" y2="115" stroke="#64748b" stroke-width="2"/>
  <polygon points="418,110 428,115 418,120" fill="#64748b"/>
  <rect x="100" y="210" width="600" height="70" rx="10" fill="#eff6ff" stroke="#93c5fd" stroke-width="1.5"/>
  <text x="400" y="237" text-anchor="middle" font-size="13" font-weight="bold" fill="#1e40af">The Attention Budget Principle</text>
  <text x="400" y="260" text-anchor="middle" font-size="11" fill="#1e40af" font-style="italic">"Find the smallest possible set of high-signal tokens that maximize</text>
  <text x="400" y="275" text-anchor="middle" font-size="11" fill="#1e40af" font-style="italic">the likelihood of the desired outcome."</text>
</svg>
```

</div>

### System Prompts: Find the Right Altitude

Anthropic identifies a "Goldilocks zone" for system prompt specificity:

| Failure Mode | Problem | Solution |
|-------------|---------|----------|
| **Too specific** (hardcoded if-else logic) | Brittle, high maintenance | Provide heuristics, not hard rules |
| **Too vague** (high-level hand-waving) | Model assumes shared context that doesn't exist | Be specific enough to guide behavior |
| **Right altitude** | Specific enough to guide, flexible enough for heuristics | Start minimal, add based on failure modes |

Best practices:
- Organize into distinct sections (`<background_information>`, `<instructions>`, `## Tool guidance`)
- Use XML tagging or Markdown headers for delineation
- Strive for the **minimal set** of information that fully outlines expected behavior
- Start with the best model + minimal prompt, then add instructions based on observed failures

### Context Retrieval: Just-in-Time vs. Pre-Computed

| Strategy | How It Works | Pros | Cons |
|----------|-------------|------|------|
| **Pre-Computed (Classic RAG)** | Embed data upfront, retrieve by similarity at query time | Fast, predictable cost | Stale indexes, irrelevant context floods attention |
| **Just-in-Time (Agentic)** | Maintain lightweight references; agent dynamically loads data using tools | Always fresh, only relevant context enters window | Slower, requires well-designed tools |

Anthropic's Claude Code uses a **hybrid approach**: `CLAUDE.md` files are dropped into context upfront, while `glob` and `grep` tools allow just-in-time navigation — bypassing stale indexing issues.

### Three Techniques for Long-Horizon Context

| Technique | How It Works | Best For |
|-----------|-------------|----------|
| **Compaction** | Summarize conversation near context limits, reinitialize with the summary | Extensive back-and-forth tasks |
| **Structured Note-Taking** | Agent writes persistent notes outside context (e.g., `NOTES.md`, to-do lists) | Iterative development with milestones |
| **Sub-Agent Architectures** | Specialized sub-agents handle focused tasks with clean context windows, return condensed summaries | Complex research, parallel exploration |

**Compaction example from Claude Code:** The model summarizes message history, preserving architectural decisions, unresolved bugs, and implementation details while discarding redundant tool outputs. It continues with compressed context plus the five most recently accessed files.

**Structured note-taking example from Claude playing Pokémon:** The agent maintains precise tallies across thousands of game steps ("for the last 1,234 steps I've been training my Pokémon in Route 1, Pikachu has gained 8 levels toward the target of 10"), develops maps of explored regions, and remembers combat strategies — all persisted outside the context window.

---

## Lesson 5: Build Effective Tool Ecosystems

Anthropic's tool design article introduces a paradigm shift: **tools are a new kind of software** — a contract between **deterministic systems** and **non-deterministic agents**. When a user asks "Should I bring an umbrella today?", an agent might call the weather tool, answer from general knowledge, or ask a clarifying question. This non-determinism requires fundamentally rethinking how we write software.

### Five Principles for Effective Tools

**Principle 1: Choose the Right Tools to Implement**

More tools ≠ better outcomes. Don't just wrap every API. Build few, thoughtful tools for high-impact workflows:

| Instead of... | Build... |
|--------------|----------|
| `list_users` + `list_events` + `create_event` | `schedule_event` (handles availability + creation) |
| `read_logs` (returns everything) | `search_logs` (returns relevant lines + context) |
| `get_customer` + `list_transactions` + `list_notes` | `get_customer_context` (compiles all relevant info) |

**Principle 2: Namespace Your Tools**

Group related tools under common prefixes to delineate boundaries:
- By service: `asana_search`, `jira_search`
- By resource: `asana_projects_search`, `asana_users_search`

Prefix- vs suffix-based naming has non-trivial effects on evaluations — test this choice.

**Principle 3: Return Meaningful Context**

Prioritize contextual relevance over flexibility. Avoid low-level technical identifiers:
- Use `name`, `image_url`, `file_type` — **not** `uuid`, `256px_image_url`, `mime_type`
- Resolving UUIDs to natural language names **significantly reduces hallucinations**
- Expose a `response_format` enum (`"concise"` vs `"detailed"`) to let agents control verbosity

**Principle 4: Optimize for Token Efficiency**

- Implement pagination, filtering, truncation with sensible defaults
- Claude Code: **25,000 token default limit** per tool response
- Steer agents toward small, targeted searches instead of single broad ones
- Prompt-engineer error responses to communicate **specific, actionable improvements** (not opaque tracebacks)

**Principle 5: Prompt-Engineer Your Tool Descriptions**

Tool descriptions are loaded into the agent's context — they collectively steer behavior. Think of describing your tool to a new hire:
- Make implicit context explicit (specialized query formats, niche terminology, resource relationships)
- Use unambiguous parameter names: `user_id` not `user`
- **Impact:** Claude Sonnet 3.5 achieved SWE-bench SOTA after precise tool description refinements
- **Impact:** A tool-testing agent that rewrote descriptions achieved **40% decrease** in task completion time

### The Evaluation-Driven Tool Improvement Loop

Anthropic's most powerful technique: **use agents to improve the tools you give them**.

1. **Build a prototype** of your tools (use Claude Code to one-shot them)
2. **Create evaluation tasks** grounded in real-world uses — strong tasks require multiple tool calls
3. **Run the evaluation** with simple agentic loops (`while`-loops wrapping alternating LLM + tool calls)
4. **Analyze results** — read transcripts, observe confusion, track tool-calling metrics
5. **Let Claude analyze transcripts and refactor tools** — paste eval transcripts into Claude Code
6. **Repeat** until strong performance on held-out test sets

This process yielded improvements **beyond expert human-written tools** in Anthropic's Slack and Asana evaluations.

---

## Lesson 6: Harness Long-Running Agents

For tasks spanning hours or days, agents face the "shift change" problem: each new context window starts with no memory of what came before. Anthropic developed a two-part solution.

### The Initializer + Coding Agent Pattern

<div align="center">

```svg
<svg viewBox="0 0 860 400" xmlns="http://www.w3.org/2000/svg" font-family="Segoe UI, Arial, sans-serif">
  <defs>
    <marker id="arr" markerWidth="8" markerHeight="6" refX="7" refY="3" orient="auto">
      <polygon points="0 0, 8 3, 0 6" fill="#475569"/>
    </marker>
  </defs>
  <text x="430" y="28" text-anchor="middle" font-size="17" font-weight="bold" fill="#1e293b">Long-Running Agent Architecture</text>
  <rect x="30" y="50" width="370" height="180" rx="12" fill="#eff6ff" stroke="#3b82f6" stroke-width="2"/>
  <text x="215" y="75" text-anchor="middle" font-size="14" font-weight="bold" fill="#1e40af">Initializer Agent (Session 1)</text>
  <text x="50" y="98" font-size="10" fill="#374151">Creates the foundation for all future sessions:</text>
  <rect x="50" y="110" width="330" height="20" rx="4" fill="#dbeafe"/>
  <text x="60" y="125" font-size="10" fill="#1e3a8a">Feature list (JSON) — all features marked "failing"</text>
  <rect x="50" y="135" width="330" height="20" rx="4" fill="#dbeafe"/>
  <text x="60" y="150" font-size="10" fill="#1e3a8a">claude-progress.txt — log of what agents have done</text>
  <rect x="50" y="160" width="330" height="20" rx="4" fill="#dbeafe"/>
  <text x="60" y="175" font-size="10" fill="#1e3a8a">init.sh — script to run the development server</text>
  <rect x="50" y="185" width="330" height="20" rx="4" fill="#dbeafe"/>
  <text x="60" y="200" font-size="10" fill="#1e3a8a">Initial git commit — baseline for all future work</text>
  <line x1="400" y1="140" x2="450" y2="140" stroke="#475569" stroke-width="2" marker-end="url(#arr)"/>
  <text x="425" y="132" text-anchor="middle" font-size="9" fill="#64748b">then</text>
  <rect x="460" y="50" width="370" height="180" rx="12" fill="#f0fdf4" stroke="#16a34a" stroke-width="2"/>
  <text x="645" y="75" text-anchor="middle" font-size="14" font-weight="bold" fill="#166534">Coding Agent (Every session after)</text>
  <text x="480" y="98" font-size="10" fill="#374151">Each session follows this sequence:</text>
  <rect x="480" y="110" width="330" height="18" rx="4" fill="#dcfce7"/>
  <text x="490" y="124" font-size="9" fill="#14532d">1. Run pwd, read progress file + git log</text>
  <rect x="480" y="132" width="330" height="18" rx="4" fill="#dcfce7"/>
  <text x="490" y="146" font-size="9" fill="#14532d">2. Read feature list, choose highest-priority feature</text>
  <rect x="480" y="154" width="330" height="18" rx="4" fill="#dcfce7"/>
  <text x="490" y="168" font-size="9" fill="#14532d">3. Run init.sh, test basic functionality first</text>
  <rect x="480" y="176" width="330" height="18" rx="4" fill="#dcfce7"/>
  <text x="490" y="190" font-size="9" fill="#14532d">4. Implement ONE feature, test end-to-end</text>
  <rect x="480" y="198" width="330" height="18" rx="4" fill="#dcfce7"/>
  <text x="490" y="212" font-size="9" fill="#14532d">5. Git commit + update progress + mark feature done</text>
  <rect x="30" y="255" width="800" height="130" rx="10" fill="#fef2f2" stroke="#fca5a5" stroke-width="1.5"/>
  <text x="430" y="280" text-anchor="middle" font-size="14" font-weight="bold" fill="#991b1b">Four Failure Modes and Solutions</text>
  <text x="50" y="305" font-size="10" fill="#374151" font-weight="bold">1. Agent tries to one-shot everything</text>
  <text x="430" y="305" font-size="10" fill="#6b7280">Feature list forces incremental, one-at-a-time work</text>
  <text x="50" y="325" font-size="10" fill="#374151" font-weight="bold">2. Agent declares "done" prematurely</text>
  <text x="430" y="325" font-size="10" fill="#6b7280">Pass/fail flags; "do not remove tests" instructions</text>
  <text x="50" y="345" font-size="10" fill="#374151" font-weight="bold">3. Agent leaves environment broken</text>
  <text x="430" y="345" font-size="10" fill="#6b7280">Git commits + progress notes; start with basic test</text>
  <text x="50" y="365" font-size="10" fill="#374151" font-weight="bold">4. Agent skips proper testing</text>
  <text x="430" y="365" font-size="10" fill="#6b7280">Explicitly prompt for browser automation (Puppeteer)</text>
</svg>
```

</div>

### Key Insights for Long-Running Agents

- **Use JSON for feature lists** — models are less likely to inappropriately modify JSON compared to Markdown
- **Git is your state management system** — agents can revert bad changes and recover working states
- **Always test before implementing** — start each session with a basic end-to-end test to catch broken state from the previous session
- **Explicitly require end-to-end testing** — without prompting, agents tend to declare features complete after writing code without verifying them as a real user would
- **Long-horizon conversation management** — agents should summarize completed work phases and store essential information in external memory before proceeding to new tasks
- **Subagent output to filesystem** — subagents can write outputs directly to external systems (files, databases), then pass lightweight references back to the coordinator, preventing information loss during multi-stage processing

---

## Lesson 7: Scale with Multi-Agent Architectures

Anthropic's Research feature uses a multi-agent orchestrator-worker system where a lead agent coordinates while specialized subagents operate in parallel. The results: **multi-agent Claude Opus 4 + Sonnet 4 subagents outperformed single-agent Opus 4 by 90.2%** on their internal research eval.

### Why Multi-Agent Works

In Anthropic's analysis, **three factors explained 95% of performance variance** on BrowseComp:

1. **Token usage** (80% of variance by itself)
2. **Number of tool calls**
3. **Model choice**

Multi-agent architectures scale token usage by distributing work across agents with separate context windows. Each subagent explores extensively but returns only a **condensed summary**.

### The Token Economics

| Interaction Type | Relative Token Usage |
|-----------------|---------------------|
| Chat | 1× (baseline) |
| Single Agent | ~4× |
| Multi-Agent | ~15× |

Multi-agent systems burn through tokens fast. They require **high-value tasks** where performance gains justify the cost. Best fit: tasks involving heavy parallelization, information exceeding single context windows, and interfacing with numerous complex tools.

### The Architecture: Orchestrator-Worker

```
User Query
    ↓
[Lead Researcher Agent] ← Extended thinking for planning
    ↓ saves plan to Memory
    ├── [Subagent 1: "AI startups 2025"] ← interleaved thinking after each tool result
    ├── [Subagent 2: "Enterprise AI adoption"] ← parallel web search
    └── [Subagent 3: "AI regulation landscape"] ← independent context window
    ↓ condensed findings returned
[Lead Researcher Agent] ← synthesizes, decides if more research needed
    ↓ if yes → spawn more subagents
    ↓ if done →
[Citation Agent] ← processes documents, attributes sources
    ↓
Final Research Report with Citations
```

### Seven Principles for Multi-Agent Prompting

**1. Think like your agents.** Build simulations with the exact prompts and tools from your system. Watch agents work step-by-step to reveal failure modes (agents continuing when they already have results, verbose search queries, wrong tool selection).

**2. Teach the orchestrator how to delegate.** Each subagent needs: an objective, an output format, guidance on tools/sources, and clear task boundaries. Without detail, agents duplicate work or leave gaps. Example failure: one subagent explored the 2021 automotive chip crisis while two others duplicated work on 2025 supply chains.

**3. Scale effort to query complexity.** Embed scaling rules in prompts:
- Simple fact-finding: 1 agent, 3-10 tool calls
- Direct comparisons: 2-4 subagents, 10-15 calls each
- Complex research: 10+ subagents with clearly divided responsibilities

**4. Tool design and selection are critical.** An agent searching the web for context that only exists in Slack is doomed. Give agents explicit heuristics: examine all available tools first, match tool usage to user intent, prefer specialized tools over generic ones.

**5. Let agents improve themselves.** Claude 4 models are excellent prompt engineers. A tool-testing agent that tested an MCP tool dozens of times and rewrote its description achieved a **40% decrease in task completion time** for future agents.

**6. Start wide, then narrow down.** Mirror expert human research: explore the landscape before drilling into specifics. Agents default to overly long, specific queries that return few results. Prompt agents to start with short, broad queries, then progressively narrow.

**7. Guide the thinking process + parallelize.** Extended thinking serves as a controllable scratchpad. Interleaved thinking after tool results helps agents evaluate quality and identify gaps. Parallel tool calling transforms speed:
- Lead agent spins up 3-5 subagents in parallel (not serially)
- Subagents use 3+ tools in parallel
- Result: **up to 90% reduction in research time** for complex queries

### Production Reliability Challenges

| Challenge | Solution |
|-----------|----------|
| **Agents are stateful; errors compound** | Build resume-from-checkpoint systems; use retry logic + regular checkpoints; let agents know when tools fail and adapt |
| **Non-deterministic debugging** | Add full production tracing; monitor agent decision patterns without reading conversation content |
| **Deployment disrupts running agents** | Use rainbow deployments — gradually shift traffic old → new while both run simultaneously |
| **Synchronous execution bottlenecks** | Consider async execution for more parallelism, but manage coordination and state consistency carefully |

---

## Lesson 8: Evaluation-Driven Development

Anthropic's evaluation article is perhaps the most practically important for anyone building production agents. Their core message: **evals are non-negotiable**, and their value compounds over the entire agent lifecycle.

### Why Evals Matter

> *"Writing evals is useful at any stage. Early on, evals force product teams to specify what success means. Later, they uphold a consistent quality bar."*

Teams without evals face **weeks of testing** when new models come out; teams with evals can upgrade in **days**. Evals also become the highest-bandwidth communication channel between product and research teams, defining metrics researchers can optimize against.

**Real-world examples:**
- **Descript** built evals around three dimensions: don't break things, do what I asked, do it well. They evolved from manual grading to LLM judges with periodic human calibration.
- **Bolt** built an eval system in 3 months that runs their agent and grades outputs with static analysis, browser agents, and LLM judges.
- **Claude Code** started with fast iteration from user feedback, then added evals — first for narrow areas like code generation, then expanding into broader behavioral evals.

### The Structure of an Agent Evaluation

An evaluation consists of:
- **Task**: An input + environment for the agent (e.g., "fix this auth bypass vulnerability")
- **Graders**: Logic that scores some aspect of the agent's performance. A task can have multiple graders with multiple assertions.
- **Transcript/Trace**: The full record of the agent's actions (tool calls, reasoning, outputs)
- **Metrics**: Quantitative measurements (turns taken, tokens used, latency)
- **Eval Suite**: A collection of tasks designed to measure specific capabilities

### Three Types of Graders

| Grader Type | Methods | Strengths | Weaknesses |
|------------|---------|-----------|------------|
| **Code-Based** | String match, binary tests, static analysis, tool call verification, transcript analysis | Fast, cheap, objective, reproducible | Brittle to valid variations, lacking nuance |
| **Model-Based** | LLM-as-judge with rubrics, pairwise comparison, multi-dimension scoring | Flexible, handles nuance, scales well | Requires calibration, non-deterministic |
| **Human** | Expert review, user testing, adversarial testing | Catches what automation misses, finds edge cases | Expensive, slow, doesn't scale |

**Best practice:** Use deterministic graders where possible, LLM graders where necessary, and human graders for validation. Don't over-specify the agent's path — grade **what the agent produced**, not the path it took.

### Capability vs. Regression Evals

<div align="center">

```svg
<svg viewBox="0 0 800 250" xmlns="http://www.w3.org/2000/svg" font-family="Segoe UI, Arial, sans-serif">
  <text x="400" y="28" text-anchor="middle" font-size="16" font-weight="bold" fill="#1e293b">Capability Evals vs. Regression Evals</text>
  <rect x="30" y="45" width="350" height="175" rx="10" fill="#eff6ff" stroke="#3b82f6" stroke-width="1.5"/>
  <text x="205" y="70" text-anchor="middle" font-size="13" font-weight="bold" fill="#1e40af">Capability / Quality Evals</text>
  <text x="50" y="92" font-size="10" fill="#374151">"What can this agent do well?"</text>
  <text x="50" y="110" font-size="10" fill="#374151">Start at LOW pass rate</text>
  <text x="50" y="128" font-size="10" fill="#374151">Target tasks the agent struggles with</text>
  <text x="50" y="146" font-size="10" fill="#374151">Give teams a "hill to climb"</text>
  <text x="50" y="164" font-size="10" fill="#374151">Direction: scores should go UP over time</text>
  <rect x="50" y="178" width="310" height="28" rx="5" fill="#dbeafe"/>
  <text x="205" y="197" text-anchor="middle" font-size="10" fill="#1e3a8a">When pass rate is high → graduate to regression suite</text>
  <rect x="420" y="45" width="350" height="175" rx="10" fill="#fef2f2" stroke="#ef4444" stroke-width="1.5"/>
  <text x="595" y="70" text-anchor="middle" font-size="13" font-weight="bold" fill="#991b1b">Regression Evals</text>
  <text x="440" y="92" font-size="10" fill="#374151">"Does it still handle what it used to?"</text>
  <text x="440" y="110" font-size="10" fill="#374151">Should have ~100% pass rate</text>
  <text x="440" y="128" font-size="10" fill="#374151">Protect against backsliding</text>
  <text x="440" y="146" font-size="10" fill="#374151">Score decline = something is broken</text>
  <text x="440" y="164" font-size="10" fill="#374151">Direction: scores should STAY at 100%</text>
  <rect x="440" y="178" width="310" height="28" rx="5" fill="#fee2e2"/>
  <text x="595" y="197" text-anchor="middle" font-size="10" fill="#991b1b">Run continuously to catch drift as you hill-climb</text>
</svg>
```

</div>

### Evaluating Different Agent Types

**Coding Agents:** Rely on well-specified tasks, stable test environments, and thorough tests. Deterministic graders are natural — does the code run and do the tests pass? SWE-bench Verified and Terminal-Bench follow this approach. LLMs progressed from 40% to >80% on SWE-bench in just one year.

**Conversational Agents:** Require a second LLM to simulate the user. Success is multidimensional: is the ticket resolved (state check), did it finish in <10 turns (transcript constraint), was the tone appropriate (LLM rubric)?

**Research Agents:** Combine groundedness checks (claims supported by sources), coverage checks (key facts included), and source quality checks (authoritative vs. first-retrieved). LLM rubrics should be frequently calibrated against expert human judgment.

**Computer Use Agents:** Require running the agent in a real or sandboxed environment and checking whether it achieved the intended outcome. Balance DOM-based interactions (fast, token-heavy) with screenshot-based interactions (slower, token-efficient).

### Handling Non-Determinism

Agent behavior varies between runs. Two metrics help:

- **pass@k**: Probability of at least one success in k attempts. As k rises, score **rises**. Useful when one success is sufficient.
- **pass^k**: Probability of ALL k trials succeeding. As k rises, score **falls**. Useful for customer-facing agents where consistency matters.

At k=1 they're identical. At k=10 they tell opposite stories: pass@k approaches 100% while pass^k approaches 0%.

### The Roadmap from Zero to Great Evals

| Step | Action | Detail |
|------|--------|--------|
| **0** | Start early | 20-50 tasks from real failures is a great start. Don't wait for hundreds. |
| **1** | Automate manual checks | Convert bug reports and manual QA into test cases. Prioritize by user impact. |
| **2** | Write unambiguous tasks | Two experts should independently reach the same pass/fail verdict. Create reference solutions. |
| **3** | Build balanced problem sets | Test both when behavior SHOULD occur and when it SHOULDN'T. Avoid class-imbalanced evals. |
| **4** | Build a robust eval harness | Each trial from a clean environment. No shared state between runs. |
| **5** | Design graders thoughtfully | Grade what was produced, not the path taken. Build in partial credit for multi-component tasks. |
| **6** | Run and iterate | Track latency, tokens, cost, error rates alongside accuracy. Calibrate LLM judges against humans. |

**Critical lesson:** Opus 4.5 initially scored 42% on CORE-Bench, but after fixing **grading bugs, ambiguous specs, and stochastic tasks**, the score jumped to **95%**. A 0% pass@100 rate usually signals a broken task, not an incapable agent.

---

## The Agent Builder's Checklist

### Phase 1: Design
- [ ] Start with the simplest possible solution (single LLM call + retrieval)
- [ ] Only add agent complexity when simpler solutions demonstrably fall short
- [ ] Choose the right pattern: workflow (predefined) vs agent (model-directed)
- [ ] Define success criteria BEFORE building — write initial eval tasks

### Phase 2: Tools & Context
- [ ] Design ACI with same care as HCI — invest heavily in tool documentation and testing
- [ ] Build few, thoughtful tools (not 1:1 API wrappers); consolidate multi-step operations
- [ ] Namespace tools clearly; test prefix vs suffix naming
- [ ] Optimize tool responses for token efficiency (pagination, filtering, truncation)
- [ ] Engineer context at every inference step — treat tokens as a finite budget
- [ ] Use hybrid retrieval: static context files + just-in-time tool-based retrieval

### Phase 3: Implementation
- [ ] Keep agent loops simple: `while` loop with LLM decision → tool execution → context update
- [ ] Implement compaction for long conversations (summarize near context limits)
- [ ] Use structured note-taking for multi-session persistence (progress files, feature lists)
- [ ] Use git for state management in long-running coding agents
- [ ] Test before implementing each session — catch broken state early

### Phase 4: Multi-Agent (Only If Needed)
- [ ] Verify the task genuinely benefits from parallelization
- [ ] Use orchestrator-worker pattern with clear delegation instructions
- [ ] Scale effort to query complexity (embed rules in prompts)
- [ ] Enable parallel tool calling for both lead agent and subagents
- [ ] Use extended thinking + interleaved thinking for planning and evaluation
- [ ] Build resume-from-checkpoint systems for error recovery

### Phase 5: Evaluation
- [ ] Start with 20-50 tasks from real usage patterns and failures
- [ ] Combine code-based, model-based, and human graders
- [ ] Maintain separate capability evals (hill to climb) and regression evals (catch drift)
- [ ] Run evals in isolated, clean environments — no shared state
- [ ] Graduate high-performing capability evals to regression suites
- [ ] Track pass@k for tasks where one success matters; pass^k where consistency matters
- [ ] Use agents to analyze eval transcripts and improve tools iteratively

---

## Conclusion

The lessons from Anthropic's six engineering articles converge on a single philosophy: **simplicity is not the starting point — it's the goal**. The teams that succeed with agents are not those who build the most complex systems, but those who find the minimum viable complexity for their task and then invest ruthlessly in the details that matter — tool design, context engineering, and evaluation.

Here are the five meta-lessons that emerge from synthesizing all six articles:

1. **Complexity is a cost, not a feature.** Every layer you add (multi-agent, long-horizon, async execution) introduces new failure modes. Add layers only when measurement proves they help.

2. **Tools are the new UX.** The Agent-Computer Interface deserves the same design rigor as a human-facing product. Anthropic spent more time on tool design than prompt design for their best agents.

3. **Context is the bottleneck.** It's not about how much you can fit in the context window — it's about the *signal-to-noise ratio* of every token. Engineer context like you engineer code: deliberately, testably, and with a bias toward deletion.

4. **Evals are the foundation.** Without evals, you're guessing. With evals, you're engineering. Start early, start small, and let your eval suite compound in value over time.

5. **Let agents improve agents.** One of the most striking patterns across Anthropic's articles is using Claude to optimize the tools, prompts, and descriptions that Claude itself uses. This self-improving loop — build → evaluate → let the agent analyze failures → refine → repeat — is the future of agent development.

The gap between prototype and production is wider than anticipated. But with these lessons from Anthropic as your guide, you have a clear roadmap for navigating that gap.

---