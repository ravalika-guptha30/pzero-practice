# LM-4 — Prompt suggestions: acceptance criteria and test scenarios

This covers the Jira story LM-4, "[LUMOS] Help users craft better prompts by offering suggestions or improvements". It is a draft for review.

**Key points**
- There are 14 acceptance criteria and 14 test scenarios. They describe what the product should do, not how it is built.
- Nothing here is checked against code. The LUMOS repository is not connected. The connected repository, `pzero-practice`, holds only a README.
- Criteria marked **(assumed)** go beyond the ticket text. Confirm them with the product owner. The open questions at the end list what the ticket does not answer.

## Source story (verbatim)

> As a user of a Gen AI writing assistance tool, I want the system to suggest improved versions of my prompts (or examples of prompts) so that I can get more accurate, relevant, and useful AI‑generated content. The suggestions should show what changes could improve clarity, specificity, or style, and allow me to accept, modify, or reject those prompt suggestions.

## Acceptance criteria

### Getting suggestions
- **AC-1** When a user has written a prompt, the system can offer one or more improved versions of it.
- **AC-2** When the user has not written a prompt yet, the system can offer example prompts.
- **AC-3** Each suggestion says which kind of improvement it makes: clarity, specificity or style.
- **AC-4** Each suggestion shows what changed compared with the original prompt, for example by highlighting the added or reworded parts.
- **AC-5** A suggestion keeps the meaning of the original prompt. It does not change the user's goal or topic.

### Accept, modify, reject
- **AC-6** Accepting a suggestion replaces the prompt text with the suggested text.
- **AC-7** Modifying a suggestion lets the user edit the suggested text before using it. Only the edited text is used.
- **AC-8** Rejecting a suggestion leaves the original prompt exactly as it was.
- **AC-9** Nothing changes the prompt until the user accepts or modifies a suggestion. The system never rewrites a prompt on its own.
- **AC-10** After accepting or modifying a suggestion, the user can go back to the original prompt. **(assumed)**

### Errors and limits
- **AC-11** If suggestions cannot be generated, for example because of a timeout or a service error, the user sees a clear message and can still use their original prompt. **(assumed)**
- **AC-12** For an empty prompt, or a prompt that is only spaces, the system does not offer "improved" versions of that prompt. It either offers examples (AC-2) or offers nothing. **(assumed)**
- **AC-13** Asking for suggestions does not submit the prompt to generate content. **(assumed)**
- **AC-14** Suggestions follow the same content-safety rules as generated content. **(assumed)**

## Test scenarios

| # | Scenario | Given | When | Then | Covers |
|---|---|---|---|---|---|
| TS-1 | Suggestion for a vague prompt | The user types "write about dogs" | They ask for suggestions | At least one improved version appears, labelled with its improvement type | AC-1, AC-3 |
| TS-2 | Changes are visible | A suggestion is shown for the user's prompt | The user views it | The added or reworded parts are shown next to the original | AC-4 |
| TS-3 | Meaning is kept | The prompt is about a product launch email | Suggestions appear | Every suggestion is still about a product launch email | AC-5 |
| TS-4 | Accept | A suggestion is shown | The user accepts it | The prompt text now equals the suggested text | AC-6 |
| TS-5 | Modify | A suggestion is shown | The user edits it and confirms | The prompt equals the edited text, not the original suggestion | AC-7 |
| TS-6 | Reject | A suggestion is shown | The user rejects it | The prompt is unchanged, character for character | AC-8 |
| TS-7 | No change without consent | Suggestions are shown | The user closes the suggestions without choosing | The prompt is unchanged | AC-9 |
| TS-8 | Undo after accept | The user accepted a suggestion | They choose to go back | The original prompt is restored | AC-10 |
| TS-9 | Examples for an empty prompt | The prompt box is empty | The user asks for help | Example prompts appear, not "improved" versions | AC-2, AC-12 |
| TS-10 | Spaces-only prompt | The prompt contains only spaces | The user asks for suggestions | No improved versions of the empty text appear | AC-12 |
| TS-11 | Service failure | The suggestion service is unavailable | The user asks for suggestions | A clear error appears and the original prompt is still usable | AC-11 |
| TS-12 | Suggestions do not run the prompt | The user has a prompt | They ask for suggestions | No content is generated until they submit | AC-13 |
| TS-13 | Several suggestions, one chosen | Three suggestions are shown | The user accepts the second one | The prompt equals the second suggestion only | AC-6 |
| TS-14 | Unsafe prompt | The prompt asks for disallowed content | The user asks for suggestions | No suggestion makes the request more effective at getting disallowed content | AC-14 |

## Open questions for the product owner

1. **Trigger:** Do suggestions appear automatically while typing, or only when the user clicks a button?
2. **Source:** Are suggestions generated by an AI model, taken from fixed templates, or both?
3. **Count:** How many suggestions should appear at once?
4. **Examples:** Where do example prompts come from, and are they tailored to the user's task?
5. **Undo:** Is going back to the original prompt required (AC-10)?
6. **Tracking:** Should accept, modify and reject choices be recorded, for analytics or to improve suggestions?
7. **Speed:** Is there a target time for suggestions to appear?
8. **Limits:** Is there a maximum prompt length for suggestions, and a usage limit per user?
9. **Language:** Are prompts in languages other than English in scope?

## What is needed to go further

Connect the LUMOS repository. Then these criteria can be checked against real code, and the scenarios can be turned into executable PlayerZero scenarios.
