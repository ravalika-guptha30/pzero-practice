# LM-8 — Graceful Handling of Ambiguous Input: Acceptance Criteria & Test Scenarios (Draft)

**Purpose:** Draft acceptance criteria and test cases for LUMOS story LM-8, ready for review.
**Status:** Draft. Not traced to code (no LUMOS repository connected). Jira did not return the ticket's Acceptance Criteria field, so compare this draft against that field before adopting it.

**Story (from Jira):** *As a* user who gives the AI an ambiguous or incomplete input, *I want* the AI to identify ambiguity, ask a concise clarification, or propose a reasonable assumption, *so that* the interaction does not devolve into multiple rounds of questions and retries.

**Key takeaways**
- Four requirements come straight from the ticket: **detect ambiguity** (AC-1), **ask a concise clarification** (AC-2), **or state a reasonable assumption** (AC-3), and **avoid multiple rounds of questions** (AC-4). Everything else is an assumption, marked **[A]**.
- The biggest open decision is the **rule for choosing** between asking and assuming. See open question 1.

---

## 1. Acceptance criteria

### Core (from the ticket)
| ID | Criterion |
|---|---|
| AC-1 | When a request has more than one materially different reading, or lacks information needed to answer, the AI recognises this before giving a full answer. |
| AC-2 | When the AI asks for clarification, it asks one short, specific question that names the missing or ambiguous point. |
| AC-3 | When a reasonable default exists, the AI may proceed with it, but it states the assumption clearly in the reply so the user can correct it. |
| AC-4 | One ambiguous request is resolved in at most one clarification round. The AI does not chain several follow-up questions across turns about the same request. |

### Choosing the right response [A]
| ID | Criterion |
|---|---|
| AC-5 | A clear, complete request is answered directly. The AI does not ask unnecessary questions. |
| AC-6 | When a wrong guess would be costly or hard to undo (for example deleting data or sending something), the AI asks instead of assuming. |
| AC-7 | When the AI asks, it offers likely options where possible (for example "Did you mean A or B?") so the user can answer in a few words. |
| AC-8 | When a request has several ambiguous points, the AI groups them into one message rather than asking about each in a separate turn. |

### After the clarification [A]
| ID | Criterion |
|---|---|
| AC-9 | After the user answers the clarification, the AI completes the original request without asking the user to repeat it. |
| AC-10 | When the user corrects a stated assumption, the AI redoes the answer with the correction and does not repeat the wrong assumption. |
| AC-11 | When the user ignores the question or replies "you decide", the AI proceeds with a stated default instead of asking again. |
| AC-12 | The AI uses earlier conversation context to resolve ambiguity, and does not ask for something the user already said. |

### Robustness [A]
| ID | Criterion |
|---|---|
| AC-13 | An empty, whitespace-only or meaningless input (for example "asdf") gets a short, friendly prompt to say what the user needs. No error is shown. |
| AC-14 | A user's answer that is itself ambiguous gets at most one more focused follow-up, then the AI proceeds with a stated assumption. |
| AC-15 | The AI does not invent specific facts (names, numbers, files) to fill gaps. Assumptions are labelled as assumptions. |

### Quality [A]
| ID | Criterion |
|---|---|
| AC-16 | Across a test set of ambiguous prompts, the average number of turns to a useful answer and the clarification rate meet targets to be agreed. |

---

## 2. Test scenarios (Given / When / Then)

### Main functionality
| ID | AC | Scenario |
|---|---|---|
| TS-1 | AC-1, AC-2 | **Given** a new conversation, **when** the user says "Book a meeting with John", **then** the AI asks one question covering what is missing (which John, when), not a full answer built on guesses. |
| TS-2 | AC-2 | **Given** the user asks "Convert this to the other format" with no format named, **when** the AI replies, **then** the reply is one short question that names the missing point (target format). |
| TS-3 | AC-3 | **Given** the user asks "Summarise this article" with no length given, **when** the AI replies, **then** it gives a summary and states the assumed length (for example "I kept it to about 5 bullet points"). |
| TS-4 | AC-4 | **Given** the user asks an ambiguous question and answers the AI's clarification, **when** the AI replies, **then** it gives the answer, not a second question about the same request. |
| TS-5 | AC-5 | **Given** a clear request such as "What is 15% of 200?", **when** the AI replies, **then** it answers directly with no clarifying question. |
| TS-6 | AC-7 | **Given** the user says "Show me the Paris weather" with Paris, France and Paris, Texas both possible, **when** the AI asks, **then** the question offers both options. |
| TS-7 | AC-8 | **Given** the user says "Create a report for the team" (report type, period and team all unclear), **when** the AI asks, **then** all open points are in one message. |
| TS-8 | AC-9 | **Given** the AI asked "Which John?" and the user replied "John Smith", **when** the AI continues, **then** it completes the booking request without asking the user to restate it. |

### Edge and negative cases
| ID | AC | Scenario |
|---|---|---|
| TS-9 | AC-6 | **Given** the user says "Delete the old files", **when** the AI replies, **then** it confirms which files before acting, rather than assuming. |
| TS-10 | AC-10 | **Given** the AI assumed "last month" for a report period, **when** the user says "No, I meant this quarter", **then** the AI redoes the report for this quarter and does not mention last month as current. |
| TS-11 | AC-11 | **Given** the AI asked a clarification, **when** the user replies "just pick one", **then** the AI proceeds with a stated default and does not ask again. |
| TS-12 | AC-12 | **Given** the user said "I work in the Sales team" earlier, **when** they later say "Make a report for my team", **then** the AI uses Sales and does not ask which team. |
| TS-13 | AC-13 | **Given** a new conversation, **when** the user sends an empty message, only spaces, or "asdf", **then** the AI replies with a short prompt asking what they need, and no error is shown. |
| TS-14 | AC-14 | **Given** the AI asked "Which date?" and the user replied "soon", **when** the AI continues, **then** it asks at most one more focused question, then proceeds with a stated assumption. |
| TS-15 | AC-15 | **Given** the user says "Email the client about the delay" with no client named, **when** the AI replies, **then** it does not invent a client name or address. It asks, or uses a clearly marked placeholder. |
| TS-16 | AC-4, AC-16 | **Given** a test set of at least 20 ambiguous prompts, **when** each is run, **then** each reaches a useful answer within the agreed number of turns, and no prompt gets more than one clarification round. |

**More edge checks to add during review:** a very long input with one small ambiguity, a mixed-language request, a typo that changes meaning ("form" vs "from"), an ambiguous pronoun ("send it to him") at the start of a conversation, and a user switching topic in reply to a clarification.

---

## 3. Assumptions
- "Ambiguous" means two or more readings that would lead to materially different answers. Minor wording choices do not count.
- Asking and assuming are both acceptable. Which one to use depends on the cost of a wrong guess (AC-6).
- Earlier context in the same conversation is available (see LM-7).
- No specific model, prompt or detection method is assumed.

## 4. Open questions for the product owner
1. What rule decides between **asking** and **assuming**? Is there a list of actions that always need confirmation?
2. Is "at most one clarification round" (AC-4) a hard rule or a target?
3. Should clarifications offer clickable options in the UI, or plain text only?
4. How should assumptions be shown: inline text, a separate note, or a UI element?
5. What should happen when the user ignores a clarification and asks something else?
6. What are the targets for clarification rate and turns-to-answer (AC-16), and how will they be measured?
7. Does this apply to every channel and language LUMOS supports?
8. What does the ticket's own Acceptance Criteria field say? Jira didn't return it, and this draft should be reconciled with it.

## 5. Grounding notes
- **Verified (read-only from Jira):** summary, type (Story), status (To Do), priority (Medium), reporter, and description. No comments or labels.
- **Not verified:** Acceptance Criteria field contents (two custom fields exist but returned no value), attachments (listing disabled), linked issues, and any LUMOS implementation.
- Scenarios are implementation-agnostic. They should be traced to code once the LUMOS repository is connected.
