---
name: pairing
description: Research a repository task, propose a small commit-by-commit implementation plan, and pair with the user through one reviewed step at a time. Use when the user wants an interactive implementation workflow with explicit plan approval, code-review handoffs, and commits made only after approval.
---

# Pairing

Work in four phases: research, planning, step implementation, and review. Preserve the approved plan and the current step across the conversation.

## Research the task

Before proposing a plan, deeply investigate the task without changing repository files.

- Read the repository's instructions and relevant documentation.
- Inspect the working tree and Git history when they help establish conventions or current state. Record pre-existing changes so they are not overwritten or committed accidentally.
- Trace the relevant implementation, tests, callers, data flow, and adjacent components. Search for existing patterns that can be reused.
- Identify the repository's formatter, linter, test commands, and any additional required CI checks.
- Form reasonable assumptions and validate them from the codebase or authoritative sources when possible.
- Do not stop to ask clarifying questions during research. Resolve ambiguity with clearly stated assumptions; the user can correct them after seeing the complete plan.

Spend enough time here to understand the intended outcome and the smallest coherent route to it. Do not implement while researching.

## Write and submit the plan

Create a uniquely named Markdown file in the system temporary directory, outside the repository. Keep it for the duration of the pairing session and report its exact path.

Start the file with concise prose explaining:

- what the task means;
- the user's likely intent and desired end state;
- the important validated assumptions and any remaining ambiguity.

Follow that prose with a Markdown checkbox list. Each top-level unchecked item (`- [ ]`) is one sequential implementation step and one future commit. Label each item with its step number and a short title. Nested bullets may explain scope, rationale, dependencies on earlier or later steps, expected files, and validation. Avoid deeply nested bullets. Use the same checkbox-list format when presenting the plan in the handoff message; do not convert the steps to ordinary bullets or a numbered list.

Design every step to be independently reviewable and as small as practical. Include only the code, tests, and documentation necessary for that step. Avoid mixing unrelated refactors with behavior changes. Use as many steps as the task genuinely needs; do not force a predetermined count.

Order related steps contiguously so work on one coherent area is completed before moving to another. Group as many related steps together in the sequence as dependencies allow, while preserving small commit boundaries. For example, if a refactor affects three services, complete all planned work for service 1 before service 2, and all work for service 2 before service 3; do not alternate between services unless a technical dependency requires it. Explain any non-obvious interleaving in the plan.

Present the complete understanding and plan to the user, along with the temporary file path, then wait for explicit approval or requested revisions. Do not begin implementation before approval. If the user requests revisions, investigate as needed, update the same plan file, present the complete revised plan, and wait again.

## Implement one step

After the user approves the plan, implement only the first uncompleted step. For every subsequent step, begin only after the preceding step has been reviewed and the user has asked to proceed.

- Reinspect the current working tree before editing. Preserve unrelated user changes and keep them out of the step's diff and commit.
- Make the minimum changes needed for the current step, following established repository patterns.
- Add or update focused tests when the step changes testable behavior.
- Review the resulting diff for correctness and scope.
- Run all checks explicitly required by the repository. At minimum, run the applicable formatter or formatting check, linter, and tests when configured. Run additional CI checks documented by the repository.
- Fix failures caused by the step. Clearly distinguish unrelated or pre-existing failures and gather enough evidence to explain them.

Never implement later plan steps merely because they are convenient to combine.

## Hand over for review

The review message must start with this exact shape, with no preamble:

`Step <current>/<total> — <brief step title>`

Immediately after the title, show a compact plan window as a checkbox list containing, in plan order:

- the previous step, when one exists, checked (`- [x]`) and identified as committed;
- the current step, unchecked (`- [ ]`) and identified as awaiting review;
- up to the next three steps, unchecked (`- [ ]`).

Do not include older completed steps or more than three future steps in this review handoff. Then provide a concise summary with a bullet for each changed file. Refer to each file by path and explain what changed and why. Also report:

- the checks run and their outcomes;
- any checks that could not run or any pre-existing failures;
- ambiguities or follow-up choices discovered during implementation;
- confirmation that no commit has been created yet.

Stop and let the user review. Treat follow-up as code review:

- Answer questions with concrete references to the implementation.
- When changes are requested, make only those changes, review the diff, rerun applicable checks, and issue an updated handoff for the same step.
- If the user edits the code, inspect and validate their changes. Do not overwrite them. Rerun relevant checks before committing.
- Do not interpret praise or a review comment as permission to commit. Wait until the user explicitly says to proceed, continue, commit, or otherwise clearly approves advancing.

## Commit and advance

When the user explicitly approves advancing:

1. Reinspect the diff and staging area. Include only changes belonging to the reviewed step; never sweep unrelated changes into the commit.
2. If the code changed since the last successful checks, rerun the applicable checks before committing.
3. Create one non-amended commit for the step. Use a clear imperative title and always include a meaningful commit body describing the change and its rationale.
4. Verify that the commit contains the intended files and that no step-related changes were left behind. Mark the committed step checked (`- [x]`) in the temporary plan without changing its scope or wording.
5. If another plan step remains, implement that one step and hand it over for review using the same workflow. If no step remains, report the completed commits and final validation status.

If safe commit isolation is impossible—for example, unrelated changes are already staged in a way that cannot be separated without risking the user's work—do not commit. Explain the exact blocker and wait for direction.
