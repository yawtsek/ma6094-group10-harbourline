# MA6094 Capstone - Group 10 - Harbourline Community Hub

An n8n multi-agent system that ingests the Harbourline source pack, generates a WBS plan,
reviews risks, drafts weekly/monthly reports, answers evidence questions and handles Week 12
change events, with named human checkpoints before anything reaches Jira or a reader.
All data is synthetic course material.

## Live instance

Workflows run on n8n Cloud: https://group10n8n.app.n8n.cloud (instance login required;
shown live in the demo). A 10-minute end-to-end screen recording is submitted alongside
as the backup required by the brief.

## Files

(The audit-trail export referenced by the governance log is submitted with the governance deliverable.)

| File | What it is |
|---|---|
| `Stage3_lm8xynIxMuY4M8HN.json` | Stage 3 main workflow (116 nodes): intake, planning via E2, evidence QA, risk loop with Critic and verifier, human review, change intake, Jira preview/approval |
| `Reports_Critic_rvlklRemAa3exUYL.json` | Reports + Critic workflow: weekly/monthly drafts, two Critic rounds, rewrite loop |
| `W06_Change_Review_YjfnLRsw3gMthfA4.json` | Standalone change-review test workflow (isolated test route; not wired to the Stage 3 change hold) |
| `QA_Test_MeOSothWL0PwErKV.json` | Question-answering test workflow (12 questions, top-k retrieval, quote verification) |

## How to reproduce

1. In any n8n (cloud or self-hosted, v1.60+), create a project and import each JSON:
   Workflows -> Import from File.
2. Add credentials: a Google Gemini API key (model `gemini-3.5-flash-lite`) and, for the
   Jira path, a Jira Cloud API token for a sandbox project.
3. Create three data tables named `HCH_shared_memory`, `HCH_audit_log`, `HCH_jira_drafts`
   and re-point the data-table nodes at them (table IDs are instance-specific).
4. Run the trigger `Self-Test: Run Sample Pack` in the Stage 3 workflow. It uses the bundled
   sample pack, stops at the Human Review form, and writes nothing to Jira.

## Notes for reviewers

- Agents run on Gemini 3.5 Flash-Lite (free tier). Approvals in the exported audit log are
  labelled exactly as they were made (test batches vs attributed decisions); see the
  governance log in the submission for how to read the approver column.
- The Week 12 change-event texts used in testing are the team's labelled paraphrases of the
  instructor memos.
- AI use: workflow agents are Gemini; Claude was used as a team tool to build and operate the
  test harness; ChatGPT assisted with document drafting. Full declaration in the AI-use statement.
