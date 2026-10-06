# CRM Email-to-Ticket Automation — Spec-Driven Development (SDD)
## Version 2.0 — Revised for Exact Original Email Preservation

**Project:** Candidate Support CRM / Email-to-Ticket & Reporting System  
**Document type:** Software Requirements + Technical SDD  
**Status:** Draft / Build Baseline  
**Primary requirement source:** Client-provided CRM requirement document  
**Critical clarification added:** Query Statement must preserve the candidate email body A-to-Z; it must not be summarized, rewritten, paraphrased, corrected, or AI-generated.

---

# 1. Executive Summary

The system is a Candidate Support CRM that brings candidate email into the CRM, creates/manages tickets, maintains campaign/post/query information, supports controlled Open → Reopen → Closed lifecycle, allows individual and bulk closing, stores complete ticket history, provides replies from CRM, and generates configurable Excel reports.

The CRM is not merely an email viewer. Email is the entry point into a structured ticket-management system.

## Core flow

Candidate Email  
→ CRM Email Inbox  
→ Ticket Creation  
→ Ticket ID  
→ Campaign/Post  
→ Candidate Details  
→ Query Type  
→ **Query Statement = complete original email body A-to-Z**  
→ Open  
→ PM Response  
→ Reply from CRM  
→ Individual/Bulk Close  
→ Candidate replies  
→ Reopen  
→ PM Response  
→ Close  
→ Reports  
→ Excel

---

# 2. Critical Business Rule — Original Email Preservation

## 2.1 Query Statement rule

When a candidate email is converted into a ticket, the system MUST preserve the complete original email content.

The Query Statement field must contain the email body exactly as received, subject to only the technical representation required to store/render the message.

### MUST NOT

- summarize the email
- paraphrase the email
- rewrite sentences
- correct grammar
- remove sentences
- shorten the query
- infer a replacement query
- use AI to generate a substitute query
- silently discard meaningful content

### MUST

- preserve the complete original body
- preserve line breaks where technically possible
- preserve the original text/content
- retain the original email separately as an immutable source where possible
- make the original email available from the ticket

## 2.2 Recommended storage

Store both:

1. `query_statement_original` — complete original email body
2. `raw_email_body` / original MIME or provider message reference — original source for audit/recovery

The CRM can display a sanitized HTML version for safe rendering, but the stored original content must remain available.

---

# 3. AI Requirement

## 3.1 AI is NOT mandatory

The first production version does not require AI to extract Query Statement.

The email body can be obtained directly through Gmail API or Microsoft 365/Outlook integration and stored as the original query.

## 3.2 Deterministic extraction

For fields that can be safely extracted from email metadata or structured patterns:

- Sender email → email provider
- Date/time → email provider
- Subject → email provider
- Message ID → email provider
- Thread ID → email provider
- Full email body → email provider
- Contact number → optional rule/regex extraction
- Application number → optional rule/pattern extraction

## 3.3 Query Type

Query Type remains a controlled CRM field selected from Master Data. It is separate from Query Statement.

Example:

**Query Type:** Payment Related

**Query Statement:**
Complete original candidate email A-to-Z.

AI may be introduced later as an optional classification assistant, but it must never replace or modify the original Query Statement.

---

# 4. Goals

1. Replace manual Gmail → copy/paste → spreadsheet workflow.
2. Create a centralized CRM email inbox.
3. Automatically create tickets from candidate emails.
4. Generate unique Ticket IDs.
5. Preserve complete original email content.
6. Maintain campaign and post information.
7. Support controlled Open/Reopen/Closed workflow.
8. Support PM responses.
9. Reply to candidates from CRM.
10. Support individual close.
11. Support bulk close.
12. Preserve closure and reopen history.
13. Provide dashboard and management reports.
14. Export reports to Excel.
15. Provide drag-and-drop report builder.
16. Provide role-based access and auditability.
17. Keep Master Data configurable instead of hard-coded.

---

# 5. Out of Scope for MVP

Unless separately approved:

- AI-generated responses
- AI rewriting of candidate emails
- automatic AI summarization replacing original content
- autonomous decision-making on ticket closure
- deleting original email content
- uncontrolled status changes

---

# 6. User Roles

## Agent

- View assigned tickets
- Create tickets
- Review email
- Reply to candidates
- Change permitted statuses
- Add/update ticket information
- Close individual tickets
- Participate in bulk close where authorized
- View own reports

## PM

- View tickets
- Provide PM Response
- Approve/guide resolution
- Review ticket history
- View reports

## QA

- Review tickets
- Check responses
- Audit history
- Export reports

## Admin

- Full access
- User management
- Campaign Master
- Post Master
- Query Type Master
- Status Master
- Email Action Master
- Email configuration
- Reporting configuration
- System settings

---

# 7. Authentication & Authorization

Implement:

- Login
- Role-based access control
- Session/token management
- Permission checks on backend
- Secure password storage if local authentication is used
- Audit logging for privileged actions

---

# 8. CRM Modules

```text
CRM
├── Dashboard
├── Email Inbox
├── Create Ticket
├── All Tickets
├── Open Tickets
├── Reopened Tickets
├── Closed Tickets
├── Ticket History
├── Reports
│   ├── Daily Report
│   ├── Campaign Report
│   ├── Agent Report
│   └── Report Builder
├── Export to Excel
└── Settings
    ├── Campaign Master
    ├── Post Master
    ├── Query Type Master
    ├── Status Master
    ├── Email Action Master
    └── User Management
```

---

# 9. Dashboard

Dashboard KPIs:

- Today's Tickets
- Total Tickets
- Open
- Reopen
- Closed
- Pending

Campaign-wise table:

| Campaign | Open | Reopen | Closed | Total |
|---|---:|---:|---:|---:|

Additional recommended widgets:

- Recent tickets
- Unassigned tickets
- Tickets awaiting PM response
- Tickets due for response
- Recent activity
- Email processing status

---

# 10. Email Integration

Supported provider direction:

- Gmail API
- Microsoft 365 / Outlook API

The final provider must be confirmed with the client.

## Incoming email workflow

```text
Candidate
   ↓
Email Provider
   ↓
CRM Email Integration
   ↓
Email Inbox
   ↓
Ticket Creation
   ↓
Unique Ticket ID
   ↓
Ticket Database
```

## Required email metadata

- Provider message ID
- Thread ID/conversation ID
- Sender
- Recipient
- CC
- BCC where available
- Date/time
- Subject
- HTML body
- Plain-text body
- Attachments metadata
- Provider labels/status where available

---

# 11. Email Inbox

Features:

- Search
- Campaign filter
- Status filter
- Date filter
- Refresh
- Ticket number
- Sender email
- Subject
- Status
- Select checkbox
- Individual open
- Bulk selection
- Bulk close
- Bulk reopen where authorized
- Export

Clicking an email/ticket opens the complete conversation and ticket details.

---

# 12. Ticket Creation

Automatically generated:

- Ticket ID
- Date/time received
- Email metadata
- Original email body

Example:

```text
Ticket: TKT-000245
Date: 06-Oct-2026
Email: candidate@example.com
Subject: Unable to download admit card
Status: Open
```

---

# 13. Ticket Fields

Recommended Tickets table:

| Field | Type | Rule |
|---|---|---|
| Ticket ID | Unique ID | Auto-generated |
| Date | DateTime | Email received date |
| Campaign | FK/Dropdown | Master Data |
| Campaign Post | FK/Dropdown | Post Master |
| Candidate Name | Text | Editable/derived |
| Email | Email | From email metadata |
| Email Reverted | Dropdown | Yes/No |
| Email Action | Dropdown | Master Data |
| Query Type | Dropdown | Master Data |
| Query Statement | Long Text | **Complete original email body** |
| Contact No | Text | Optional extracted/entered |
| Application No | Text | Optional extracted/entered |
| Status | Dropdown | Controlled workflow |
| Open Query | Long Text | Initial action/details |
| PM Response | Long Text | PM response |
| Created By | User | Audit |
| Assigned To | User | Assignment |
| Closed Date | DateTime | Set on close |
| Closed By | User | Set on close |
| Closing Remark | Long Text | Especially bulk close |

---

# 14. Query Statement — Exact Requirement

This is a high-priority acceptance rule.

### Example input

```text
Dear Team,

I am Rahul Sharma and I am facing an issue with my application.
My application number is APP123456.
I completed the payment yesterday but the portal is still showing
payment pending.

I have attached the receipt for your reference.
Please check and help me.

Regards,
Rahul Sharma
```

### CRM Query Statement

```text
Dear Team,

I am Rahul Sharma and I am facing an issue with my application.
My application number is APP123456.
I completed the payment yesterday but the portal is still showing
payment pending.

I have attached the receipt for your reference.
Please check and help me.

Regards,
Rahul Sharma
```

It must not become:

```text
Payment pending issue.
```

and it must not become an AI summary.

---

# 15. Query Type

Query Type is a separate classification/dropdown.

Example Master values:

- Application Related
- Payment Related
- Eligibility
- Admit Card
- City Intimation
- Login Issue
- Password Issue
- Document Related
- Technical Issue
- Correction / Edit
- Result Related
- Refund Related
- Other

These values must be configurable through Master Data.

---

# 16. Campaign Master

Example values from client requirement:

- HPRCA
- RRB
- MPHC
- GSSSB
- IAF
- DHC
- DCCB
- NDMS
- NBMS
- KRCL
- SBC
- Other

Do not hard-code these values.

---

# 17. Status Master

Default values:

- Open
- Reopen
- Closed
- Pending

If the client requires only three statuses:

- Open
- Reopen
- Closed

The status list must be configurable.

---

# 18. Controlled Ticket Lifecycle

## New ticket

```text
Email arrives
↓
Ticket created
↓
Status = OPEN
```

## Resolution

```text
PM Response
↓
Agent reviews
↓
Reply to candidate
↓
Email Action = Replied
↓
Close
```

## Candidate replies after closure

```text
Closed
↓
Candidate replies
↓
Reopen
↓
New history entry
↓
PM Response
↓
Reply
↓
Close
```

---

# 19. Ticket History

Do not depend on only:

- Reopen 1
- Reopen 2
- Reopen 3

Instead create a separate Ticket History table.

Suggested fields:

| History ID | Ticket ID | Status | Query/Action | PM Response | Date | Updated By |
|---|---|---|---|---|---|---|

This supports unlimited reopen cycles.

---

# 20. Email Reply From CRM

Ticket view must provide:

- To
- Subject
- Reply editor
- Send Email

After successful send:

- Email Reverted = Yes
- Email Action = Replied
- Sent message saved against ticket
- Conversation updated
- Audit event created

---

# 21. Individual Close

Ticket detail page:

```text
Status: [Open ▼]

PM Response:
[................................]

Email Action:
[Replied ▼]

[Send Reply]   [Close Ticket]
```

Clicking Close Ticket:

```text
Are you sure you want to close this ticket?

[Cancel] [Confirm Close]
```

On confirmation:

- Status = Closed
- Closed Date = current timestamp
- Closed By = logged-in user
- Closing audit event created

---

# 22. Bulk Close

Available from:

- All Tickets
- Open Tickets
- Email Inbox

Each row has checkbox.

Toolbar:

```text
[Select All]

Selected: 3

Bulk Action: [Close Selected]
```

Confirmation:

```text
You are about to close 3 tickets.

[Cancel] [Confirm Close]
```

---

# 23. Bulk Close Validation

Before closing, validate every selected ticket.

Block if:

- PM response missing
- Required response pending
- Email reply requirement not satisfied
- Already closed
- User lacks permission
- Ticket belongs to another restricted team, if applicable

Example:

```text
3 tickets selected

✓ TKT-001 — Ready to close
✓ TKT-002 — Ready to close
✕ TKT-003 — PM Response missing
```

Only valid tickets may be closed.

---

# 24. Bulk Close Remark

Recommended mandatory field:

```text
Bulk Close Remark:
[................................................]

[Confirm Bulk Close]
```

The remark is saved against every successfully closed ticket.

---

# 25. Bulk Close Audit

For each ticket:

| Ticket | Previous Status | New Status | Closed By | Closed Date | Remark |
|---|---|---|---|---|---|
| TKT-001 | Open | Closed | Agent | timestamp | Response provided |
| TKT-002 | Open | Closed | Agent | timestamp | Response provided |

Bulk close must create an individual audit record for each ticket.

---

# 26. Reopen

When a candidate responds after closure:

```text
Closed
↓
Candidate replies
↓
Reopen
```

Previous closure information must remain intact.

New history entry must capture:

- Reopen date
- Reopen reason/query
- Updated by
- New PM response
- New email interaction

The reopened ticket can subsequently be closed again.

---

# 27. Ticket Detail Page

Premium CRM layout should show:

### Header

- Ticket ID
- Status
- Campaign
- Priority/other approved metadata if later required
- Assigned user
- Actions

### Candidate panel

- Name
- Email
- Contact
- Application number

### Original Email

Display the **complete original email A-to-Z**.

### Query metadata

- Query Type
- Campaign
- Post
- Email Action
- Email Reverted

### PM response

Editable according to role.

### Conversation

Full email thread.

### Ticket timeline

```text
Email Received
↓
Ticket Created
↓
Open
↓
PM Response
↓
Email Replied
↓
Closed
↓
Reopened
↓
PM Response
↓
Closed
```

---

# 28. Excel Reports

Report page:

```text
Date From
Date To
Campaign
Status

[Generate Report]
[Export Excel]
```

Excel should contain ticket-level information.

Core fields:

- Date
- Ticket
- Campaign
- Post
- Name
- Email
- Email Reverted
- Email Action
- Query Type
- Query Statement
- Contact No
- Application No
- Status
- Open Query
- PM Response
- Closed Date
- Closed By
- Closing Remark

Important: Query Statement exported to Excel must contain the **complete original email body**, not a summary.

---

# 29. Recommended Excel Workbook

## Sheet 1 — Ticket Report

Contains the latest/current ticket state.

## Sheet 2 — Ticket History

Contains every lifecycle/history event.

This allows complete auditability.

---

# 30. Drag-and-Drop Report Builder

UI:

```text
Available Fields       Report Columns

DATE                   DATE
TICKET                 TICKET
CAMPAIGN               CAMPAIGN
NAME                   NAME
EMAIL                  STATUS
STATUS                 QUERY TYPE
QUERY TYPE             PM RESPONSE
PM RESPONSE
```

Manager can drag fields into the desired order.

Actions:

- Generate Report
- Preview
- Export Excel

The generated Excel must follow the selected column order.

---

# 31. Search & Filters

Global search should support:

- Ticket ID
- Candidate name
- Email
- Contact number
- Application number
- Subject
- Query text where practical

Filters:

- Campaign
- Post
- Query Type
- Status
- Email Action
- Assigned To
- Date range

---

# 32. Duplicate Detection

The system should detect possible duplicates using provider message/thread IDs first.

Potential duplicate signals:

- Same provider Message ID
- Same thread ID
- Same sender
- Same application number
- Similar subject/time window

Do not delete data automatically. Flag for review where confidence is insufficient.

---

# 33. Database Architecture

Recommended:

**PostgreSQL**

Core tables:

```text
users
roles
permissions
tickets
ticket_history
ticket_emails
email_attachments
campaigns
campaign_posts
query_types
statuses
email_actions
audit_logs
report_definitions
```

---

# 34. Ticket Email Storage

Each ticket should support multiple email events.

Suggested fields:

- email record ID
- ticket ID
- provider message ID
- provider thread ID
- direction: inbound/outbound
- sender
- recipients
- cc
- subject
- body text
- body HTML
- received/sent timestamp
- attachment references

This preserves the complete conversation instead of only one email.

---

# 35. API Architecture

Suggested backend:

**FastAPI** or **Node.js**

Core API groups:

```text
/auth
/users
/tickets
/tickets/{id}
/tickets/{id}/history
/tickets/{id}/emails
/tickets/{id}/reply
/tickets/{id}/close
/tickets/bulk-close
/tickets/{id}/reopen
/email/inbox
/email/sync
/reports
/reports/export
/report-builder
/master-data
/audit
```

---

# 36. Email Sync Architecture

Use provider APIs rather than storing Gmail/Outlook passwords.

```text
Email Provider
      ↓
OAuth/API Authorization
      ↓
Sync Worker
      ↓
Message Deduplication
      ↓
Ticket Matching
      ↓
New Ticket OR Existing Ticket/Reopen
      ↓
Database
      ↓
CRM UI
```

---

# 37. Existing Ticket Matching

When an email arrives, system should attempt:

1. Provider thread/conversation ID
2. Existing ticket message references
3. Candidate email + application number
4. Other approved matching rules

If it matches a closed ticket and is a legitimate follow-up:

```text
Existing Ticket
↓
Candidate Reply
↓
Reopen
↓
Add Ticket History
```

If no match:

```text
Create New Ticket
```

---

# 38. Email Processing Reliability

Every incoming message should have a processing state:

- Received
- Processing
- Processed
- Failed
- Needs Review

Failed emails must not silently disappear.

Provide retry/reprocess capability for authorized users.

---

# 39. Security

Requirements:

- OAuth for email provider
- No Gmail/Outlook password stored
- HTTPS
- Secure session/token handling
- Backend authorization
- Role-based permissions
- Input validation
- HTML email sanitization
- Safe attachment handling
- Audit logs
- Database backups
- Secrets stored outside source code
- Environment variables/secret manager

---

# 40. Original Email Security + Rendering

Email HTML may contain unsafe content.

Therefore:

- Sanitize HTML before displaying
- Preserve raw source separately
- Do not execute scripts from email
- Do not allow email HTML to alter CRM UI
- Keep original source for audit/recovery

Rendering security must never modify the canonical stored original.

---

# 41. UI/UX Direction

The CRM must NOT be designed as a basic/simple CRUD application.

Required direction:

- Premium enterprise CRM
- Modern dashboard
- Professional dark sidebar
- Light main workspace
- Strong visual hierarchy
- KPI cards
- Charts
- Status badges
- Ticket timeline
- Rich email conversation view
- Advanced tables
- Filters
- Search
- Bulk action toolbar
- Confirmation dialogs
- Responsive layout
- Smooth but restrained animations
- Empty/loading/error states
- Professional client-presentation quality

The visual language should remain consistent across:

- Dashboard
- Email Inbox
- Ticket Detail
- Create Ticket
- Reports
- Report Builder
- Master Data
- User Management
- Email Integration

---

# 42. Automation

Phase 1 automation:

- Automatic Ticket ID
- Email-to-ticket creation
- Email metadata extraction
- Complete original email preservation
- Ticket matching
- Status lifecycle
- Email reply tracking

Phase 2:

- Automated reopen detection
- Duplicate detection
- Response/SLA tracking
- Escalation alerts

AI is optional and must not replace deterministic preservation.

---

# 43. AI Optional Future Layer

If later approved:

AI may assist with:

- Query Type suggestion
- Campaign/Post suggestion
- Candidate field extraction
- Confidence scoring
- Suggested categorization

But:

```text
Original Email
      ↓
Immutable Original
      ↓
AI Suggestion
      ↓
Human/Rule Validation
      ↓
Structured Fields
```

AI must never overwrite the original email/query statement.

---

# 44. Reporting

Required reports:

- Daily report
- Campaign report
- Agent report
- Status report
- Reopened ticket report
- Closed ticket report
- Ticket history
- Custom report builder

Every report should support appropriate filters.

---

# 45. Audit Trail

Audit important actions:

- Ticket created
- Ticket assigned
- Status changed
- PM response added/updated
- Email sent
- Ticket individually closed
- Ticket bulk closed
- Ticket reopened
- Master Data changed
- User permission changed
- Report generated/exported

Audit should capture:

- User
- Timestamp
- Action
- Entity
- Entity ID
- Old value where relevant
- New value where relevant

---

# 46. Error Handling

UI must clearly show:

- Email sync failed
- Ticket creation failed
- Email send failed
- Bulk close partially failed
- Invalid master data
- Permission denied
- Network error
- Duplicate detected
- Ticket not found

Never silently fail.

---

# 47. Bulk Operation Result

After bulk close:

```text
Bulk Close Completed

Successfully closed: 18
Blocked: 2
Failed: 1

View Details
```

Blocked/failed tickets should remain unchanged.

---

# 48. Development Phases

## Phase 1 — Core CRM

- Login
- Roles
- Dashboard
- Create Ticket
- Ticket List
- Ticket Detail
- Open/Reopen/Closed
- Search/filter
- Master Data

## Phase 2 — Email

- Gmail/Microsoft authorization
- Inbox sync
- Email-to-ticket
- Original email preservation
- Conversation storage
- Reply from CRM

## Phase 3 — Management

- PM response
- Assignment
- Ticket History
- Reopen automation
- Individual close
- Bulk close
- Audit

## Phase 4 — Reporting

- Daily report
- Campaign report
- Agent report
- Filters
- Excel export
- Drag-and-drop report builder

## Phase 5 — Advanced Automation

- Duplicate detection
- SLA
- Escalation
- Optional AI assistance
- Advanced notifications

---

# 49. Testing Strategy

## Email tests

- Plain-text email
- HTML email
- Long email
- Email with signature
- Email with attachment
- Forwarded email
- Replied email
- Threaded email

## Original-content test

Given an email body, the stored Query Statement must match the original content.

Acceptance condition:

**No summarization or content loss.**

## Ticket tests

- Create
- Assign
- Reply
- Close
- Reopen
- Close again
- Multiple reopens

## Bulk close tests

- All valid
- Some invalid
- All invalid
- Already closed
- Missing PM response
- Missing reply where required
- Permission restriction
- Partial failure

## Excel tests

- Correct columns
- Correct rows
- Complete Query Statement
- Correct history
- Correct closing data
- Correct filters
- Drag/drop column order

---

# 50. Key Acceptance Criteria

The MVP is acceptable only when:

1. Candidate email can enter CRM.
2. Ticket is created with unique Ticket ID.
3. Original email body is preserved A-to-Z.
4. Query Statement is not summarized or rewritten.
5. Query Type remains a separate configurable field.
6. Campaign/Post are configurable.
7. Ticket can move through controlled lifecycle.
8. PM response can be stored.
9. Reply can be sent from CRM.
10. Individual close works.
11. Bulk close works.
12. Bulk close validates selected tickets.
13. Closing date/user are recorded.
14. Closed ticket can reopen.
15. Previous closure information remains.
16. Unlimited ticket history is supported.
17. Email conversation is retained.
18. Reports can be filtered.
19. Excel export works.
20. Query Statement in Excel contains complete original email content.
21. Report Builder supports drag/drop fields.
22. Roles and permissions work.
23. Audit records are retained.
24. Email failures are visible and retryable.

---

# 51. Final System Flow

```text
                    CANDIDATE
                       │
                       │ EMAIL
                       ▼
                ┌───────────────┐
                │  EMAIL INBOX  │
                └───────┬───────┘
                        │
                        ▼
                ┌───────────────┐
                │ CREATE TICKET │
                └───────┬───────┘
                        │
             ┌──────────┼──────────┐
             ▼          ▼          ▼
        Campaign     Candidate   Query Type
          /Post       Details
             │          │          │
             └──────────┼──────────┘
                        ▼
              ORIGINAL EMAIL BODY
                 A-TO-Z PRESERVED
                        │
                        ▼
                  STATUS: OPEN
                        │
                        ▼
                  PM RESPONSE
                        │
                        ▼
                REPLY TO CANDIDATE
                        │
              ┌─────────┴─────────┐
              ▼                   ▼
       INDIVIDUAL CLOSE       BULK CLOSE
              │                   │
              └─────────┬─────────┘
                        ▼
                     CLOSED
                        │
                        ▼
              CANDIDATE REPLIES
                        │
                        ▼
                     REOPEN
                        │
                        ▼
                  PM RESPONSE
                        │
                        ▼
                     CLOSED
                        │
                        ▼
                 TICKET HISTORY
                        │
                        ▼
                  REPORT BUILDER
                        │
                        ▼
                   EXCEL EXPORT
```

---

# 52. Final Build Principle

The most important implementation principle is:

> **Store the truth first, structure it second, automate third.**

The candidate's original email is the source of truth.

Therefore:

**Original Email ≠ AI Summary**

**Query Statement = Original Email Content**

Structured fields such as Query Type, Campaign, Post, Status, Application Number, Contact Number, etc. may be populated separately, but the original candidate communication must remain preserved.

---

# 53. Client Confirmation Items Before Development

Before coding, confirm:

1. Gmail/Google Workspace or Microsoft 365/Outlook?
2. Exact email mailbox to connect?
3. Should every incoming email automatically create a ticket?
4. Exact Ticket ID format?
5. Exact Campaign/Post mapping rules?
6. Exact Query Type Master values?
7. Exact Status values?
8. Whether Pending is required?
9. Whether email reply is mandatory before close?
10. Whether PM approval is mandatory before close?
11. Who can bulk close?
12. Whether bulk close remark is mandatory?
13. Which fields are mandatory?
14. Whether complete email including signature should be shown/stored in Query Statement?
15. Required Excel columns?
16. Required report formats?
17. User/role list?
18. Hosting preference?
19. Data retention period?
20. Attachment storage requirement?

---

# 54. Definition of Done

The project is complete when the client can:

**Receive candidate email → see it in CRM → create/match ticket → preserve the complete email A-to-Z → classify/query fields → assign → obtain PM response → reply from CRM → close individually or in bulk → reopen when candidate replies → retain complete history → generate filtered reports → drag/drop report columns → export accurate Excel.**

**Version 2.0 critical change:** No AI is required for original Query Statement extraction. The complete original email body is preserved directly from the connected email provider.
