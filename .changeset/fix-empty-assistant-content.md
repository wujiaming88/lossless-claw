---
"@martian-engineering/lossless-claw": patch
---

Fix crash when assistant message has empty content

`contentFromParts()` returned an empty array `[]` when both message parts
and fallback content were empty for assistant messages. Providers like
Anthropic reject messages with zero ContentBlock entries, causing:

> The content field in the Message object at messages.0 is empty.

This can happen when:
- A stored assistant message has no parts and empty content text
- Tool-call-only assistant turns lose all blocks after orphan filtering

Two fixes applied:
1. `contentFromParts()` now always returns at least one text block for
   assistant messages instead of an empty array.
2. `assemble()` filters out any assistant messages that still end up with
   empty content arrays as a safety net (e.g. after
   `filterNonFreshAssistantToolCalls` removes all tool-call blocks).
