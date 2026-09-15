# Architecture and journeys

## System architecture

```mermaid
flowchart LR
    Teams[Microsoft Teams] --> Agent[Copilot Studio Shadowing Agent]
    Agent --> AgentActions[Copilot Studio actions]
    AgentActions --> SP[(SharePoint Lists)]
    AgentActions --> Notify[Teams private notifications / Approvals]

    Mentor[Mentor] --> MentorFlow[Per-mentor scheduled Power Automate flow]
    MentorFlow --> OutlookCalendar[Office 365 Outlook calendar]
    MentorFlow --> OutlookMail[Office 365 Outlook mail]
    MentorFlow --> AI[Approved AI classifier]
    MentorFlow --> PrivateCandidates[Private Candidates list]
    MentorFlow --> Published[Published Opportunities list]

    Coordinator[Pilot coordinator] --> CoordinatorFlows[Coordinator flows]
    CoordinatorFlows --> Requests[Shadow Requests list]
    CoordinatorFlows --> Imports[Import Batches]
    CoordinatorFlows --> Audit[Audit History]

    Exports[Approved ESXP/Shadow App export] --> ImportFlow[Import preview and processing flow]
    ImportFlow --> Imports
    ImportFlow --> PrivateCandidates
```

## Mentor automated discovery journey

```mermaid
sequenceDiagram
    actor M as Mentor
    participant A as Copilot Studio Agent
    participant F as Mentor-owned scheduled flow
    participant O as Outlook
    participant AI as Approved AI classifier
    participant SP as SharePoint

    M->>A: Become a mentor
    A->>SP: Create/update mentor profile and scanner config
    A->>M: Explain data access and activation steps
    M->>F: Activate template using own Outlook connection
    F->>SP: Confirm mentor enrolled and scanning enabled
    F->>O: Read upcoming calendar window
    F->>O: Search bounded related email context
    F->>AI: Classify only eligible correlated candidates
    F->>SP: Store private candidate summary and version
    F->>M: Send private review digest if new/material changes exist
    M->>A: Approve exact proposed listing
    A->>SP: Publish exact reviewed version
```

## Learner request and coordinator journey

```mermaid
sequenceDiagram
    actor L as Learner
    actor M as Mentor
    actor C as Coordinator
    participant A as Copilot Studio Agent
    participant SP as SharePoint
    participant W as Power Automate

    L->>A: Find opportunities
    A->>SP: Search eligible published opportunities
    A->>L: Show safe listing details
    L->>A: Request a place
    A->>W: Submit request with idempotency key
    W->>SP: Reserve capacity using version check
    W->>M: Ask mentor to approve learner request
    M->>W: Approve or decline
    W->>SP: Update request state
    W->>C: Create coordinator invitation task
    C->>SP: Record permission check and invitation outcome
```

## Role boundaries

| Role | Allowed actions |
| --- | --- |
| Learner | Manage learner profile, search published opportunities, request/withdraw own place, view own request status, pause recommendations. |
| Mentor | Manage mentor profile, scanning preferences, private candidates, own listings, and requests for own listings. |
| Pilot coordinator | Review mentor-approved requests, check attendance permissions, coordinate invitations with authorized organizer, manage imports and operational issues. |
| Administrator | Configure lists, permissions, retention, environments, DLP-compatible connectors, and flows. |

Authorization is derived from authenticated identity and Entra/SharePoint group membership. Email address, mentor ID, opportunity ID, role, or approval payload values supplied by chat are never trusted as proof of authorization.

