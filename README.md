# Inline Review

Turn a long agent response into a precise revision request. Comment on exact
passages as you read, then send all of your feedback together.

Agents often produce work that is easier to review in place: an implementation
plan, a proposed approach, a detailed explanation, or a long generated draft.
When you have several separate questions or changes, squeezing them into one
follow-up paragraph makes both the feedback and its target harder to follow.

Inline Review lets you select assistant text, attach a comment or request its
removal, and keep each passage marked while you continue reading. Add an
overall note when needed, review the collected items, and send one structured
message back to the same thread. The agent receives every instruction beside
the passage it refers to, while the result remains readable in ordinary chat
history.

## When it helps

- Review and refine a plan before asking the agent to implement it.
- Ask separate questions about several parts of a technical discussion.
- Comment on multiple claims or sections in a report, draft, or explanation.
- Request targeted removals while preserving the overall direction of a response.

## How it works

### 1. Select the passage

Select text in an assistant response and choose **Feedback** from BB's native
selection menu. You can keep reading instead of stopping to compose a large
follow-up message from memory.

![Selected assistant text with the Feedback action highlighted](docs/screenshots/inline-review-01-select-feedback.png)

### 2. Attach the instruction

Write a comment tied to that exact quote. Choose **Comment**, or choose
**Remove** when the passage should be left out of the next revision.

![The feedback editor with a comment about invitation expiry](docs/screenshots/inline-review-02-add-comment.png)

### 3. Collect feedback while you read

Saved comments remain marked in yellow, and removal requests use a red
strike-through. The compact banner shows how many items are waiting without
covering the conversation.

![Several staged comments highlighted in a long implementation plan](docs/screenshots/inline-review-03-staged-feedback.png)

### 4. Review the complete request

Expand the banner to edit individual items, remove mistakes, and add an
overall note that applies to the whole response. Nothing is sent until you
choose **Send feedback**.

![Three passage comments and overall feedback ready to send](docs/screenshots/inline-review-04-review-draft.png)

### 5. Send one readable message

Inline Review delivers the entire review back to the same thread as one compact,
numbered message. Each instruction stays beside its quote, so the agent and the
human reading the chat can understand what should change.

![The structured Review feedback message after delivery](docs/screenshots/inline-review-05-delivered-feedback.png)

## Install

```sh
bb plugin install git:https://github.com/rdanhau/bb-plugin-inline-review.git@semver:^1.0.0
```

Inline Review 1.0.1+ supports BB 0.43.1 and later, including BB 0.44.

## Develop

```sh
npm ci
bb plugin types --check
npm test
npm run typecheck
bb plugin build
bb plugin install . --yes
```

After edits, run `bb plugin build` and `bb plugin reload inline-review`, or use
`bb plugin dev`. The development SDK pin is 0.5.29; the manifest supports BB
0.43.1 and later with Plugin SDK 0.4.87 or later.
Tests include the SDK's public-import scanner.

## Behavior details

- **Comment** saves the selected quote with an instruction. Return also saves;
  Shift+Return inserts a new line where available.
- **Remove** asks the agent to disregard the quote and accepts an optional
  reason. It does not alter the original assistant message.
- The Feedback banner remains hidden while the draft is empty. Expand it to
  edit or delete items and add **Overall feedback**. Clear asks for confirmation
  before discarding the complete draft.
- Ctrl+Enter or ⌘+Enter sends from the overall-feedback field. Delivery uses
  BB's `auto` policy (sent, queued, or steered). The draft clears only after BB
  accepts the request, and a failed request preserves it.
- The outbound message uses a compact numbered format. Comment items contain
  the quoted passage followed by **Comment:**. Removal items use **Remove:**
  followed by the quote and an optional **Reason:**. Internal IDs and sequence
  numbers are never included in the message sent to the agent.

The plugin retains the exact browser range during the current session. After
BB recreates the page, it restores a highlight only when the quote has one
whitespace-normalized match across visible rendered assistant messages. This
allows selections to span Markdown blocks without treating an inactive tab's
retained DOM as a duplicate. Repeated or otherwise ambiguous passages remain
saved and reviewable without a highlight. Browsers without the CSS Custom
Highlight API retain the complete feedback workflow without passage coloring.

## Storage and limits

Saved drafts live in plugin-owned KV storage on your BB server, keyed by thread ID. They survive browser and plugin reloads. The plugin has no external service; submitted feedback enters ordinary thread history and reaches that thread's configured agent provider. Quotes, feedback, message IDs and sequence references are stored, never complete source messages. Unsaved text in the floating editor is transient.

Limits enforced on every RPC and persisted draft: 20,000 characters per quote, 10,000 per annotation body, 20,000 overall, 100 annotations, and 220,000 UTF-8 serialized bytes per draft. Whitespace-only comments/selections are rejected. Deleted-thread events remove drafts. Threads deleted while the plugin is disabled may leave one bounded orphan draft until plugin data is removed.

Concurrent operations are serialized per thread. A second simultaneous send sees the cleared draft and cannot send it again. Different tabs refresh through realtime notifications; simultaneous edits to the same field use the last accepted value. Delivery and KV storage are separate SDK operations: an ambiguous transport/server interruption after host acceptance cannot provide transactional exactly-once guarantees. Check thread history before retrying an uncertain delivery.

## V1 boundaries

Assistant text selection only. No user-message, file, preview, diff, or replacement-renderer annotation. Highlighting uses the public content-script lifecycle and CSS Highlight API without changing rendered message nodes. Reconstructing a range relies on BB's rendered assistant-Markdown containers until the SDK provides persistent selection anchors. The floating card remains app-level because the SDK does not expose selection geometry. BB currently also displays registered message actions in message action bars, including user messages; those invocations show selection guidance and create no annotation. There is no public visibility filter for this surface.

The SDK provides message ID and exact quote, without character offsets. Identical passages within one message cannot be distinguished by occurrence. Source lines are blockquoted in the batch; Comment, optional Reason, and overall feedback text are user instructions. No approval gate, agent tool, settings page, CLI, archive, or annotation-resolution workflow is registered.

See `VERIFICATION.md` for observed live results and remaining verification limits.

## Project information

- [Manual mobile and desktop acceptance checks](docs/TEST_CHECKLIST.md)
- [Prepared marketplace entry](docs/MARKETPLACE_ENTRY.json)
- [Release verification](VERIFICATION.md)
- [Version history](CHANGELOG.md)
- [Security and privacy](SECURITY.md)

Inline Review is available under the [MIT License](LICENSE).
