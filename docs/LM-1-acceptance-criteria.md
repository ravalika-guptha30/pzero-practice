# LM-1 — AI-Drafted Support Responses with Human Review: Acceptance Criteria & Test Scenarios (Draft)

**Purpose:** Draft acceptance criteria and test scenarios for LUMOS story LM-1, ready for review.
**Status:** Draft. Not traced to code (no LUMOS repository connected). The ticket has no separate Acceptance Criteria text, so everything beyond the description is an assumption.

**Story (from Jira):** *As a* customer service user, *I want* the system to use a generative AI model to draft responses to common support queries, *so that* I can get faster, consistent, and helpful replies without waiting long for manual human responses. The AI should understand the context of the conversation, suggest a draft response which a human agent can edit or approve, and flag uncertain or complex issues for manual handling.

**Key takeaways**
- Four requirements come straight from the ticket: **draft replies to common queries** (AC-1), **use conversation context** (AC-2), **human agent can edit or approve** (AC-3, AC-4), and **flag uncertain or complex issues** (AC-5). Everything else is an assumption, marked **[A]**.
- The biggest open decision is whether a draft can **ever reach the customer without a human approving it**. This draft assumes it cannot (AC-6).

---

## 1. Acceptance criteria

### Core (from the ticket)
| ID | Criterion |
|---|---|
| AC-1 | For a common support query, the system produces a draft reply that answers the customer's question. |
| AC-2 | The draft uses the context of the whole conversation (earlier messages, details the customer already gave), not only the latest message. |
| AC-3 | A human agent can edit the draft before it is sent. The customer receives the edited text, not the original draft. |
| AC-4 | A human agent can approve the draft as-is, and it is then sent to the customer. |
| AC-5 | When the query is uncertain or complex, the system flags it for manual handling instead of presenting it as a ready-to-send draft. |

### Human-in-the-loop safety [A]
| ID | Criterion |
|---|---|
| AC-6 | No AI draft is sent to the customer without an explicit agent action (approve, or edit and send). |
| AC-7 | An agent can reject or discard a draft and write the reply manually. |
| AC-8 | A flagged conversation shows the agent why it was flagged (for example low confidence, sensitive topic, multiple issues). |

### Quality and consistency [A]
| ID | Criterion |
|---|---|
| AC-9 | Drafts follow the support team's tone and style guidelines. Similar queries get consistent answers. |
| AC-10 | Drafts do not invent facts, such as order details, policies, or promises the knowledge base or conversation does not support. |
| AC-11 | Drafts are written in the customer's language. |
| AC-12 | Drafts do not reveal another customer's data or internal-only information. |

### Speed and fallback [A]
| ID | Criterion |
|---|---|
| AC-13 | A draft is available to the agent fast enough to beat the current manual response time. The threshold is to be agreed. |
| AC-14 | If the AI service is slow or unavailable, the agent can still reply manually, and the conversation is not blocked. |

### Tracking [A]
| ID | Criterion |
|---|---|
| AC-15 | Each sent reply records whether it was approved as-is, edited, or written manually, so draft quality can be measured. |

---

## 2. Test scenarios (Given / When / Then)

| ID | AC | Scenario |
|---|---|---|
| TS-1 | AC-1 | **Given** a customer asks "How do I reset my password?", **when** the agent opens the conversation, **then** a draft reply with the reset steps is shown. |
| TS-2 | AC-2 | **Given** the customer gave their order number two messages earlier, **when** they ask "Where is it now?", **then** the draft refers to that order without asking for the number again. |
| TS-3 | AC-3 | **Given** a draft is shown, **when** the agent changes a sentence and sends, **then** the customer receives the edited text. |
| TS-4 | AC-4 | **Given** a correct draft is shown, **when** the agent approves it, **then** the customer receives that exact draft. |
| TS-5 | AC-5 | **Given** a customer describes a billing dispute and a technical fault in one message, **when** the system processes it, **then** the conversation is flagged for manual handling. |
| TS-6 | AC-5 | **Given** a query the system has low confidence on, **when** the agent opens it, **then** it is marked as flagged and not shown as ready to send. |
| TS-7 | AC-6 | **Given** a draft is generated, **when** no agent acts on it, **then** nothing is sent to the customer. |
| TS-8 | AC-7 | **Given** a draft is shown, **when** the agent discards it, **then** the agent can write and send a manual reply, and the draft is not sent. |
| TS-9 | AC-8 | **Given** a conversation is flagged, **when** the agent opens it, **then** the flag reason is visible. |
| TS-10 | AC-9 | **Given** two customers ask the same refund-policy question, **when** drafts are generated, **then** both drafts give the same policy answer in the agreed tone. |
| TS-11 | AC-10 | **Given** the customer asks about a policy not in the knowledge base, **when** a draft is generated, **then** it does not state an invented policy, or the conversation is flagged. |
| TS-12 | AC-11 | **Given** the customer writes in Spanish, **when** a draft is generated, **then** the draft is in Spanish. |
| TS-13 | AC-12 | **Given** Customer A asks about "my last order", **when** a draft is generated, **then** it contains only Customer A's order details. |
| TS-14 | AC-13 | **Given** a common query, **when** the agent opens the conversation, **then** the draft appears within the agreed time. |
| TS-15 | AC-14 | **Given** the AI service is unavailable, **when** the agent opens a conversation, **then** the agent can still reply manually, and a clear "draft unavailable" state is shown. |
| TS-16 | AC-15 | **Given** three replies were sent (one approved, one edited, one manual), **when** the records are reviewed, **then** each reply shows the correct outcome. |

**Negative and edge checks to add during review:** an empty or one-word customer message, an abusive or threatening message, a message with personal or payment data, a very long conversation history, and two agents opening the same draft at once.

---

## 3. Assumptions
- "Common support queries" means queries the knowledge base or past answers cover. Anything else is a candidate for flagging (AC-5).
- The draft is shown only to the agent. The customer never sees an unapproved draft (AC-6).
- Existing data-handling and privacy policies apply. This story adds no new storage rules (AC-12).
- No specific model, prompt, or confidence method is assumed.

## 4. Open questions for the product owner
1. Can any draft ever be sent automatically without agent approval, for example for very simple queries?
2. Which queries count as "common"? Is there a list or a knowledge-base scope?
3. What makes an issue "uncertain or complex"? Who sets the confidence threshold?
4. Should flagged conversations still get a draft as a starting point, or no draft at all?
5. What is the target response time (AC-13), and how will "faster" be measured against today?
6. Which channels are in scope: chat, email, or both?
7. Which languages must be supported (AC-11)?
8. Should agent edits feed back to improve future drafts?

## 5. Grounding notes
- **Verified:** ticket summary, type (Story), status (To Do), priority (Medium), and description, read-only from Jira. The ticket has no labels and no comments.
- **Not verified:** components, linked issues, attachments, and any LUMOS implementation.
- Scenarios are implementation-agnostic. They should be traced to code once the LUMOS repository is connected.
