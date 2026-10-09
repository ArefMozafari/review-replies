---
name: review-replies
description: Work through review comments on the user's own pull request or merge request one thread at a time - check each claim against the code, propose the exact edit, and draft a reply in the team's own language and register, while the user decides every edit, commit, push, and posted reply. Use when review comments land on the user's PR or MR and they want them handled - "new review", "reviewer commented", "someone replied", "requested changes", "address the review", "pipeline failed on my MR". GitHub through gh, GitLab through glab.
---

# Review replies

One thread at a time, checked against the code before a word is said, in words a tired colleague
can follow. The user decides every edit, every commit, every reply.

## Setup

Ask for the PR or MR number when it was not given. Fetch the open threads into a scratch file and
keep it; every later step reads it.

GitHub keeps line comments, review summaries, and conversation comments apart, so fetch all three:

```bash
gh api --paginate --slurp "repos/<owner>/<repo>/pulls/<n>/comments" > line-comments.json
gh api --paginate --slurp "repos/<owner>/<repo>/pulls/<n>/reviews" > reviews.json
gh api --paginate --slurp "repos/<owner>/<repo>/issues/<n>/comments" > conversation.json
```

GitLab returns every discussion in one call:

```bash
glab api --paginate --output ndjson "projects/<id>/merge_requests/<n>/discussions" > threads.ndjson
```

List them in the reviewer's order: reviewer, `path:line`, one-line gist. Say how many. Then
present the first one.

## One item at a time

Present exactly one thread, then stop and wait for the user's word. No edits, no drafts for later
items, no "while I am here". The next item starts only after the user says so.

Every item message is these four parts, in this order:

1. **Plain words.** Three sentences, each under twenty words, then stop: what the reviewer says;
   what the code shows, with the file and line read; the verdict: right, right but the suggested
   fix breaks, or wrong. No term the reviewer did not use. History, counts across packages,
   pipeline state, and what to weigh stay out unless the claim is about them.
2. **Exact edit.** The replacement text or snippet, never described in prose only. When a sibling
   doc or file repeats the same claim, list it too.
3. **Reply draft.** In the language and register the team uses in its own threads, not the
   language of this one comment. Short: what was found, what was done. Identifiers, paths, and
   commands on their own line in backticks. When the reviewer is wrong: the evidence, no verdict
   on the reviewer.
4. **One line** naming exactly what the user's go-ahead applies: which edit, in which file.

Commit text comes after the last item, never inside one.

Before part 1: read the lines named, count references, check a signature, run the small thing the
claim is about. Fetching, git history, and builds happen only when the claim is about them. A
reviewer's suggested fix is a claim too; trace it (does it compile, does the test still pass)
before accepting it.

Something else found on the way is one sentence at the end of the item, offered, never applied.

## After the last item

- Commits: one concern each, subject and body in the repo's own style. A file carrying two
  concerns is staged by hunk (`git apply --cached` with a patch per concern). Stop before commit
  and before push; each needs the word.
- Push, then post the replies, one per thread, from the saved drafts. Posting needs its own word.
- Read the pipeline. Red: open the failing job's log and quote the deciding line before touching
  anything.
- If the agent keeps memory or notes for the project, record the round there: commits, pipeline
  result, decisions the user made.

## Red flags

| Thought | Reality |
|---|---|
| "All three are small, I'll do them together" | The user reads one at a time. Batching hides the decision. |
| "The reviewer knows this code" | Reviewers misread lines too. Read the line. |
| "I fixed the neighbouring bug while there" | Offered, not applied. Scope is the user's. |
| "Analyze passed, so I ran the full setup" | Verify the claim, not the world. |
| "The report is long because the finding is deep" | Three sentences, then the edit. Depth is a tangent. |
| "The commit message belongs with the item" | It belongs after the last item, once. |
| "The reviewer wrote English, so English" | The team's own threads decide the language, not this comment. |
