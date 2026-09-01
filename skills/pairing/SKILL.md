---
name: pairing
description: Research a repository task, propose a small PR/commit/step implementation plan, and pair with the user through one reviewed commit at a time. Use when the user wants an interactive implementation workflow with explicit plan approval, code-review handoffs, and commits made only after approval.
---

# Pairing

Work in four phases: research, planning, commit implementation, and review. Preserve the approved plan, current PR, and current commit across the conversation.

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

Follow that prose with this hierarchy:

- `### PR <number> — <short title>` headings group work into reviewable deliveries.
- Under each PR, every top-level unchecked item (`- [ ] Commit <number>: <short title>`) is one sequential future commit.
- Under each commit, nested bullets labeled as steps describe the work needed to complete that commit. Steps may cover scope, rationale, dependencies, expected files, automated validation, and manual testing. Avoid deeply nested bullets.

Use one PR group by default. Split the plan into multiple PRs when separate review and landing points would reduce risk, keep changes understandable, or deliver useful work independently. Keep dependencies between PRs explicit. A planned PR boundary does not by itself authorize publishing a PR or performing other external actions.

Use the same PR, commit, and step hierarchy when presenting the plan in later handoff messages. Do not call commits steps or steps substeps.

Design every commit to be independently reviewable and as small as practical. Include only the code, tests, and documentation necessary for that commit. Give each commit as many concrete steps as its work genuinely needs; do not force a predetermined count. Avoid mixing unrelated refactors with behavior changes.

Plan for frequent runnable milestones where the user can manually exercise the integrated work, not only review code. Prefer vertical slices that connect enough layers to demonstrate real behavior, especially for UI work. Do not let a long sequence of isolated foundation steps accumulate without integration; when foundational work must come first, keep that sequence as short as practical and schedule the next user-testable integration point explicitly. In the relevant plan items, include how the user will run the work, what they can try, and what behavior they should expect.

Order related commits contiguously so work on one coherent area is completed before moving to another. Group related commits into the same PR when dependencies and reviewability support it, while preserving small commit boundaries. For example, if a refactor affects three services, complete all planned commits for service 1 before service 2, and all work for service 2 before service 3; do not alternate between services unless a technical dependency requires it. Explain any non-obvious interleaving or PR split in the plan.

Present the complete understanding and plan to the user, along with the temporary file path, then wait for explicit approval or requested revisions. Do not begin implementation before approval. If the user requests revisions, investigate as needed, update the same plan file, present the complete revised plan, and wait again.

## Implement one commit

After the user approves the plan, implement only the first uncompleted commit by completing its listed steps. For every subsequent commit, begin only after the preceding commit has been reviewed and the user has asked to proceed.

- Reinspect the current working tree before editing. Preserve unrelated user changes and keep them out of the current commit's diff.
- Make the minimum changes needed to complete the current commit's steps, following established repository patterns.
- Add or update focused tests when the commit changes testable behavior.
- Review the resulting diff for correctness and scope.
- Run all checks explicitly required by the repository. At minimum, run the applicable formatter or formatting check, linter, and tests when configured. Run additional CI checks documented by the repository.
- Fix failures caused by the commit. Clearly distinguish unrelated or pre-existing failures and gather enough evidence to explain them.

Never implement steps from later commits merely because they are convenient to combine.

## Hand over for review

The review message must start with this exact shape, with no preamble:

`PR <current-pr>/<total-prs> · Commit <current-commit>/<commits-in-pr> — <brief commit title>`

Immediately after the title, show a compact plan window using the plan's PR headings and commit checkboxes. Preserve the current commit's nested steps. Include, in plan order:

- the previous commit, when one exists, checked (`- [x]`) and identified as committed;
- the current commit, unchecked (`- [ ]`) and identified as awaiting review;
- up to the next three commits, unchecked (`- [ ]`), adding their PR headings when the window crosses a PR boundary.

Do not include older completed commits or more than three future commits in this review handoff. Then provide a concise summary with a bullet for each changed file. Refer to each file by path and explain what changed and why. Also report:

- the checks run and their outcomes;
- any checks that could not run or any pre-existing failures;
- manual testing instructions for the current integrated state, including how to run it, what to try, and the expected behavior; if the commit is not yet user-testable, explain why and identify the next planned integration checkpoint;
- ambiguities or follow-up choices discovered during implementation;
- confirmation that no commit has been created yet.

Stop and let the user review. Treat follow-up as code review:

- Answer questions with concrete references to the implementation.
- When changes are requested, make only those changes, review the diff, rerun applicable checks, and issue an updated handoff for the same commit.
- If the user edits the code during handover, treat those edits as part of the current commit: inspect and validate them, do not overwrite them, and rerun relevant checks before committing. Include them in the commit unless the user explicitly identifies them as unrelated.
- Do not interpret praise or a review comment as permission to commit. Wait until the user explicitly says to proceed, continue, commit, or otherwise clearly approves advancing.

## Commit and advance

When the user explicitly approves advancing:

1. Reinspect the diff and staging area. Include the agent's work and any user changes made during handover for the reviewed commit. Keep clearly unrelated changes out of the commit; if ownership or intent is ambiguous, resolve it from the review conversation or ask before committing.
2. If the code changed since the last successful checks, rerun the applicable checks before committing.
3. Create the one non-amended commit represented by the current plan item. Use a clear imperative title and always include a meaningful commit body describing the change and its rationale.
4. Verify that the commit contains the intended files and that no current-commit changes were left behind. Mark the commit checked (`- [x]`) in the temporary plan without changing its scope, steps, or wording.
5. If another commit remains in the current PR, implement that commit and hand it over for review using the same workflow. At a PR boundary, report the completed PR's commits and treat the boundary as a delivery checkpoint; continue into the next planned PR only after the user explicitly asks to proceed. If no commit remains, report the completed PRs, commits, and final validation status.

If safe commit isolation is impossible—for example, unrelated changes are already staged in a way that cannot be separated without risking the user's work—do not commit. Explain the exact blocker and wait for direction.
