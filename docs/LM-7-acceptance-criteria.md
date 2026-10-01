# LM-7 — Context-Aware Continuation: Acceptance Criteria & Test Scenarios (Draft)

**Purpose:** Draft acceptance criteria and test scenarios for LUMOS story LM-7, ready for review.
**Status:** Draft. Not traced to code (no LUMOS repository connected). Jira did not return the ticket's Acceptance Criteria field, so compare this draft against that field before adopting it.

**Story (from Jira):** *I want* the AI to remember what we discussed earlier in the conversation, *so that* I don't need to repeat the background each time and the system doesn't loop back asking for context.

**Key takeaways**
- Only two requirements come straight from the ticket: **remember earlier context** (AC-1) and **don't re-ask for context already given** (AC-2). Everything else is an assumption, marked **[A]**.
- The biggest open decisions are **scope** (one conversation vs. across sessions) and **limits** (what happens in very long conversations). See the open questions.

---

## 1. Acceptance criteria

### Core (from the ticket)
| ID | Criterion |
|---|---|
| AC-1 | Within one conversation, the AI uses facts, preferences and decisions the user stated in earlier turns when answering later turns. |
| AC-2 | The AI does not ask the user for information they already gave earlier in the same conversation, unless that information is ambiguous or contradicted. |

### Correctness and updates [A]
| ID | Criterion |
|---|---|
| AC-3 | When the user corrects or replaces an earlier fact, the AI uses the newest value from then on and no longer uses the old one. |
| AC-4 | When two earlier statements conflict and the user hasn't said which one wins, the AI asks one clarifying question. It does not silently pick one. |
| AC-5 | The AI does not invent context the user never gave. It doesn't claim "you said earlier…" about something that wasn't said. |
| AC-6 | Follow-up references such as "it", "that one" or "the second option" resolve to the right earlier item. |

### Long conversations [A]
| ID | Criterion |
|---|---|
| AC-7 | Key background (goal, constraints, named entities, decisions) stays available after the conversation grows past the point where older messages are shortened or summarized. |
| AC-8 | If some earlier detail can no longer be recalled, the AI says so and asks only for that missing detail, not the whole background again. |

### Session scope and boundaries [A]
| ID | Criterion |
|---|---|
| AC-9 | When a user reopens or resumes the same conversation, the earlier context is available without being re-entered. |
| AC-10 | A new conversation starts without context from other conversations, unless cross-conversation memory is explicitly in scope and enabled. |
| AC-11 | Context from one user's conversation is never available in another user's conversation. |
| AC-12 | When the user goes back to an earlier point and takes a different path, the AI uses the context of the active path, not the abandoned one. |

### Control and transparency [A]
| ID | Criterion |
|---|---|
| AC-13 | When asked "what do you remember about this conversation?", the AI gives an accurate summary of the key context it is using. |
| AC-14 | When the user asks the AI to forget a specific item, the AI stops using that item for the rest of the conversation. |
| AC-15 | Sensitive data the user shares (for example credentials) follows the product's existing data-handling policy and is not repeated back without need. |

### Quality [A]
| ID | Criterion |
|---|---|
| AC-16 | Remembering context does not noticeably slow down responses compared with the current baseline. The threshold is to be agreed. |

---

## 2. Test scenarios (Given / When / Then)

| ID | AC | Scenario |
|---|---|---|
| TS-1 | AC-1 | **Given** in turn 1 the user said "I'm building a Python 3.11 service on AWS Lambda", **when** in turn 5 they ask "how should I package dependencies?", **then** the answer targets Python 3.11 on Lambda without asking for the language or platform. |
| TS-2 | AC-2 | **Given** the user gave their project name and goal earlier, **when** they ask a follow-up that depends on those, **then** the AI does not ask for the project name or goal again. |
| TS-3 | AC-2 | **Given** a 10-turn conversation with a stated background, **when** the user asks 5 more related questions, **then** none of the replies asks for background already given. |
| TS-4 | AC-3 | **Given** the user said "deadline is Friday", then later "actually, deadline moved to Monday", **when** they ask "how many days do I have?", **then** the AI uses Monday. |
| TS-5 | AC-4 | **Given** the user said "use PostgreSQL" and later "use MySQL" with no sign of which wins, **when** they ask for a schema, **then** the AI asks one question to confirm which database to use. |
| TS-6 | AC-5 | **Given** the user never mentioned a budget, **when** they ask for a recommendation, **then** the AI does not refer to a budget "you mentioned earlier". |
| TS-7 | AC-6 | **Given** the AI listed three options, **when** the user says "go with the second one", **then** the AI continues with option two by name. |
| TS-8 | AC-7 | **Given** a key constraint ("must run offline") was stated in turn 1 and the conversation has grown very long, **when** the user asks for an architecture, **then** the answer still respects the offline constraint. |
| TS-9 | AC-8 | **Given** a very long conversation where a minor early detail is no longer available, **when** a question depends on it, **then** the AI asks only for that detail and keeps the rest of the background. |
| TS-10 | AC-9 | **Given** the user closed a conversation after giving background, **when** they reopen it and ask a follow-up, **then** the AI answers using that background without asking for it again. |
| TS-11 | AC-10 | **Given** User A gave background in conversation 1, **when** User A starts a new conversation 2 and asks a generic question, **then** the AI does not assume conversation 1's background (unless cross-conversation memory is enabled). |
| TS-12 | AC-11 | **Given** User A shared a project name in their conversation, **when** User B asks "what project am I working on?", **then** the AI does not reveal User A's project. |
| TS-13 | AC-12 | **Given** the user went back to turn 3 and chose a different approach from the one used in turns 4–6, **when** they continue, **then** the AI uses the new approach and does not treat turns 4–6 as current decisions. |
| TS-14 | AC-13 | **Given** a conversation with a stated goal, tech stack and deadline, **when** the user asks "what do you remember so far?", **then** the summary correctly lists all three and adds nothing that wasn't said. |
| TS-15 | AC-14 | **Given** the user said "forget my company name", **when** a later answer would normally include it, **then** the AI leaves it out. |
| TS-16 | AC-16 | **Given** a long conversation with stored context, **when** the user sends a new message, **then** the response time stays within the agreed threshold compared with a short conversation. |

**Negative and edge checks to add during review:** an empty first message, a user who switches topic completely mid-conversation, a mixed-language conversation, and a user who pastes a very large document as background.

---

## 3. Assumptions
- "Conversation" means one chat thread. Memory across separate chats is **out of scope** unless the product owner says otherwise (AC-10).
- The AI may shorten or summarize older messages in long chats. That's acceptable as long as the key background survives (AC-7, AC-8).
- Existing data-retention and privacy policies apply. This story adds no new storage rules (AC-15).
- No specific model, storage or summarization method is assumed.

## 4. Open questions for the product owner
1. Is memory limited to one conversation, or should LUMOS remember users **across conversations**?
2. If across conversations: is it opt-in, how long is it kept, and can users see and delete it?
3. What counts as "key background" that must never be lost in long chats?
4. When context is partly lost, should the AI say so explicitly (AC-8), or ask quietly?
5. Should users be able to view or edit what the AI remembers (AC-13, AC-14)?
6. Which data types must **not** be remembered or repeated (credentials, personal data)?
7. What is the acceptable response-time impact (AC-16), and how will "loops back asking for context" be measured, for example a re-ask rate target?
8. What does the ticket's own Acceptance Criteria field say? Jira didn't return it, and this draft should be reconciled with it.

## 5. Grounding notes
- **Verified:** ticket summary, type (Story), status (To Do), priority (Medium) and description, read-only from Jira.
- **Not verified:** acceptance-criteria field contents, attachments, linked issues, and any LUMOS implementation.
- Scenarios are implementation-agnostic. They should be traced to code once the LUMOS repository is connected.
