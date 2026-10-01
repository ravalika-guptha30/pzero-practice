# LM-8 — Graceful Handling of Ambiguous Input: Acceptance Criteria & Test Scenarios (Draft)

**Purpose:** Draft acceptance criteria and test cases for LUMOS story LM-8, ready for review.
**Status:** Draft. Not traced to code (no LUMOS repository connected). Core criteria (AC-1 to AC-4) now match the ticket's Acceptance Criteria field, as supplied by Jampana Murthy Raju.

**Story (from Jira):** *As a* user who gives the AI an ambiguous or incomplete input, *I want* the AI to identify ambiguity, ask a concise clarification, or propose a reasonable assumption, *so that* the interaction does not devolve into multiple rounds of questions and retries.

**Key takeaways**
- Four requirements come straight from the ticket's Acceptance Criteria: **detect unclear input** (AC-1), **ask one clarifying question or offer a default for confirmation** (AC-2, AC-3), **proceed correctly without backtracking** (AC-3a), and **keep clarification loops minimal, preferably one** (AC-4). Everything else is an assumption, marked **[A]**.
- "Offers a default assumption with confirmation" means the AI proposes the default and **waits for the user to confirm** before acting on it. Confirmed by Jampana Murthy Raju.
- The biggest open decision is the **rule for choosing** between asking and offering a default. See open question 1.

---

## 1. Acceptance criteria

### Core (from the ticket)
| ID | Criterion |
|---|---|
| AC-1 | The AI identifies when the input lacks clarity, for example a missing key parameter or an ambiguous phrase, before giving a full answer. |
| AC-2 | When clarification is needed, the AI asks **one** well-phrased clarifying question that names the missing or ambiguous point. |
| AC-3 | Instead of a question, the AI may offer a default assumption and ask the user to confirm it. The AI does not act on the default until the user confirms. |
| AC-3a | Once the user responds, or confirms the assumption, the AI proceeds correctly without further backtracking: it does not reopen points already settled. |
| AC-4 | The number of follow-up clarification loops is minimal, preferably one per request. |

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
| TS-3 | AC-3 | **Given** the user asks "Generate the sales report" with no period given, **when** the AI replies, **then** it offers a default ("I'll use last month — OK?") and waits for confirmation before generating. |
| TS-3a | AC-3a | **Given** the AI offered "last month" as the default, **when** the user replies "yes", **then** the AI generates the report for last month and does not ask about the period again. |
| TS-4 | AC-3a, AC-4 | **Given** the user asks an ambiguous question and answers the AI's clarification, **when** the AI replies, **then** it gives the answer, not a second question about the same request, and it does not revisit the point the user just settled. |
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
2. Should clarifications offer clickable options in the UI, or plain text only?
3. How should assumptions be shown: inline text, a separate note, or a UI element?
4. What should happen when the user ignores a clarification and asks something else?
5. What are the targets for clarification rate and turns-to-answer (AC-16), and how will they be measured?
6. Does this apply to every channel and language LUMOS supports?
7. The ticket says loops should be "preferably one". When is a second loop acceptable (AC-14)?

## 5. Grounding notes
- **Verified (read-only from Jira):** summary, type (Story), status (To Do), priority (Medium), reporter, and description. No comments or labels.
- **Acceptance Criteria field:** the connector did not return it. The four criteria were supplied by Jampana Murthy Raju and are reflected in AC-1 to AC-4. She also confirmed the AC-3 reading: the AI waits for confirmation before acting on a default.
- **Not verified:** attachments (listing disabled), linked issues, and any LUMOS implementation.
- Scenarios are implementation-agnostic. They should be traced to code once the LUMOS repository is connected.
