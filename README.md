# MA6094 Capstone - Group 10 - Harbourline Community Hub

An n8n multi-agent system that ingests the Harbourline source pack, generates a WBS plan, reviews risks, drafts weekly/monthly reports, answers evidence questions and handles Week 12 change events, with named human checkpoints before anything reaches Jira or a reader. All data is synthetic course material.

## Live instance

Workflows run on n8n Cloud: https://group10n8n.app.n8n.cloud (instance login required; shown live in the demo). A 10-minute end-to-end screen recording is submitted alongside as the backup required by the brief.

## Files

(The audit-trail export referenced by the governance log is submitted with the governance deliverable.)

| File | What it is |
|---|---|
| `01_Harbourline_Main_Stage3.json` | Stage 3 main orchestrator (129 nodes): intake, planning via E2, evidence QA, risk loop with Critic and verifier, human review, change intake, Jira proof/approval |
| `02_Planning_Subworkflow.json` | WBS generation subworkflow (23 nodes): scope, extract, dependency, review agents with caching |
| `03_Evidence_Decision_Memory.json` | Evidence and decision memory retrieval (6 nodes): citation validation via vectorDB |
| `04_Reports_and_Critic.json` | Reports + Critic workflow (20 nodes): weekly/monthly drafts, two critic rounds, rewrite loop |
| `05_WhatIf_and_Fairness.json` | What-if scenarios and fairness analysis (14 nodes): parallel schedule/cost impact and stakeholder fairness |
| `06_QA_Evaluation.json` | QA evaluation workflow (15 nodes): 12 questions, top-k retrieval, quote verification |
| `07_Change_Review_Demo.json` | Change review demo (5 nodes): standalone change management with cite-marking and audit logging |

## How to reproduce

1. In any n8n (cloud or self-hosted, v1.80+), create a project and import each JSON workflow via **Import from file**.
2. Add credentials: a Google Gemini API key (model `gemini-2.5-flash-lite`) and, for the Jira path, a Jira cloud API token (or leave the Jira nodes inactive).

## Notes

- control-v main is n8n's internal routing node, not an LLM call.
- The built 53 change-event tools used in testing are the team's labelled paraphrases of the instructor memo.
- Use `control + shift + a` to toggle the tab key moving focus. Alternatively, use esc then tab to move to the next interactive element on the page.
