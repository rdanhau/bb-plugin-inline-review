# Changelog

All notable changes to Inline Review are documented here.

## [Unreleased]

## [1.0.1] - 2026-09-30

- Support BB 0.44 and later while retaining BB 0.43.1 compatibility.
- Develop against Plugin SDK 0.5.29 while retaining the 0.4.87 runtime API floor.

## [1.0.0] - 2026-09-17

- Annotate native assistant-message selections with comments or removal requests.
- Keep one server-side review draft per thread and deliver it as one compact,
  numbered message.
- Highlight staged comments and removals on uniquely matched response text and
  restore them across navigation.
- Support selections across Markdown block boundaries without changing the
  rendered message DOM.
- Provide a compact mobile editor and a collapsed in-chat review banner with
  Overall feedback.
- Support Return submission for comments and Ctrl+Enter or ⌘+Enter delivery
  from Overall feedback.
- Preserve drafts on delivery failure and confirm before clearing all feedback.
