# Harbourline Community Hub — n8n Workflow Export

> MA6094 Agentic AI for Project Management — Capstone Deliverable  
> Exported: 2026-11-15

## Overview

This folder contains the complete set of n8n workflows that power the **Harbourline Community Hub Refurbishment** project management agent. The system ingests a source document pack (meeting minutes, contracts, schedules, budgets), then autonomously generates a risk register, WBS, reports, what-if scenarios, fairness analysis, and supports change management — all with human-in-the-loop review gates and full audit trails.

## Architecture

```
01_Main_Stage3  (orchestrator)
 ├── 02_Planning_Subworkflow      (WBS generation & review)
 ├── 03_Evidence_Decision_Memory  (evidence retrieval & citation validation)
 ├── 04_Reports_and_Critic        (report drafting with critic loop)
 ├── 05_WhatIf_and_Fairness       (what-if scenarios & fairness analysis)
 └── 07_Change_Review_Demo        (change management review)

06_QA_Evaluation  (standalone quality assurance & quote verification)
```

## Workflow Descriptions

### 01 — Main Stage 3 Orchestrator
| File | `01_Harbourline_Main_Stage3.json` |
|------|----------------------------------|
| n8n Name | Harbourline Capstone - Stage 3 - Source Pack and Review Loop |
| Nodes | 129 |
| Status | Active |

The master workflow. Accepts a source document pack (PDF, TXT, CSV), splits and parses each file, builds an evidence pack, then orchestrates the full pipeline:

1. **Evidence & Memory** — calls W02 to retrieve and validate prior decisions
2. **WBS Planning** — calls W01 (E2) to generate and review the Work Breakdown Structure
3. **Risk Register** — multi-agent chain: Risk Draft → Omission Analyst → Risk Scorer → Register Critic → Human Review → Risk Corrector → Final Verifier
4. **Q&A Engine** — evidence-based question answering with quote location and verification
5. **Reports & Critic** — calls W04 for report generation with critic feedback loop
6. **What-if & Fairness** — calls W05 for scenario analysis and fairness checks
7. **Change Management** — impact analysis with Monte Carlo simulation, critic review, and human approval
8. **Jira Integration** — generates Jira drafts from WBS, with sandbox preview and human approval before write

Key features: human review gates at every critical decision, escalation paths, audit logging to Shared Memory data table, citation verification on all AI outputs.

---

### 02 — Planning Subworkflow (WBS Generator)
| File | `02_Planning_Subworkflow.json` |
|------|-------------------------------|
| n8n Name | Harbourline E2 - WBS Generator and Reviewer |
| Nodes | 23 |
| Status | Active |

Called by the Main Orchestrator. Generates a Work Breakdown Structure from the source pack using a multi-step agent pipeline:

1. **Scope Agent** — defines project scope from source documents
2. **Extract Agent** — extracts work items in chunks
3. **Dependency Agent** — identifies inter-task dependencies
4. **Review Agent** — validates completeness and consistency

Includes a caching layer (Cache Read/Write via data table) to avoid redundant LLM calls on identical inputs.

---

### 03 — Evidence and Decision Memory
| File | `03_Evidence_Decision_Memory.json` |
|------|-----------------------------------|
| n8n Name | W02_MEMORY - Evidence and Decision Memory |
| Nodes | 6 |
| Status | Active |

Called by the Main Orchestrator. Retrieves previously verified evidence and approved decisions from Shared Memory, optionally validates AI-generated citations via Gemini, and returns a structured contract to the caller.

---

### 04 — Reports and Critic
| File | `04_Reports_and_Critic.json` |
|------|------------------------------|
| n8n Name | Harbourline Personal - Reports + Critic (test) |
| Nodes | 20 |
| Status | Active |

Called by the Main Orchestrator. Generates project reports through a **two-round critic loop**:

1. **Round 1**: Draft Agent → Critic Agent → Pass/Fail decision
2. **Round 2** (if needed): Rewrite Agent → Critic Agent → Final decision

Reports and critic feedback are saved to Shared Memory with full audit trail.

---

### 05 — What-if and Fairness
| File | `05_WhatIf_and_Fairness.json` |
|------|-------------------------------|
| n8n Name | Harbourline Personal - What-if + Fairness (test) |
| Nodes | 14 |
| Status | Active |

Called by the Main Orchestrator. Runs two parallel analysis streams:

- **What-if Analysis** — generates schedule and cost impact scenarios with stated assumptions
- **Fairness Analysis** — computes fairness statistics across stakeholder groups

Both outputs are saved to Shared Memory with audit logs.

---

### 06 — QA Evaluation (Standalone)
| File | `06_QA_Evaluation.json` |
|------|------------------------|
| n8n Name | Harbourline Personal - Full-pack QA + quote verification (test) |
| Nodes | 15 |
| Status | Inactive |

Standalone quality assurance workflow. Loads a sample source pack, parses all files (PDF extraction, text/CSV decoding), builds an evidence pack, then runs a QA Agent that answers questions with top-k retrieval and verifies quote locations against the original documents.

Used for testing and evaluation of the evidence retrieval pipeline independently from the main orchestrator.

---

### 07 — Change Review Demo
| File | `07_Change_Review_Demo.json` |
|------|------------------------------|
| n8n Name | W06 \| HCH Change Review (Editor) |
| Nodes | 5 |
| Status | Inactive |

Lightweight change management workflow:

1. Accepts change review inputs
2. Detects the change and marks downstream outputs as stale
3. Saves the change record and human actions to Shared Memory

Designed as a simplified demo of the change management capability embedded in the Main Orchestrator.

---

## How to Import

1. Open your n8n instance
2. Go to **Workflows** → click **⋮** (menu) → **Import from File**
3. Select any `.json` file from this folder
4. The workflow will appear with all nodes, connections, and configurations intact

> **Note**: The workflows use **Google Gemini** as the LLM provider. You will need to configure your own Gemini API credentials in n8n before running them. The Shared Memory data table referenced by the workflows must also exist in your n8n project.

## Tech Stack

- **n8n** — workflow orchestration (no-code)
- **Google Gemini** — LLM for all agent nodes
- **n8n Data Tables** — persistent shared memory and audit storage
- **n8n Forms** — human review interfaces
- **Monte Carlo simulation** — schedule/cost risk quantification (JavaScript Code node)
