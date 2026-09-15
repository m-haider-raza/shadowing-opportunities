# Feasibility validation

## Verified in this Scout session

| Area | Status |
| --- | --- |
| Local file artifact generation | Available. |
| Shell execution | Available. |
| Browser automation | Available. |
| WorkIQ/Microsoft 365 tool surface | Available. |
| Microsoft 365 sign-in | Present in this session. |

This confirms this package can be authored and reviewed locally. It does not prove tenant-level Copilot Studio, Power Automate, DLP, connector, capacity, or publishing availability.

## Tenant checks required before implementation

| Check | Required result | How to validate |
| --- | --- | --- |
| Copilot Studio availability | Makers can create an agent and publish to Teams in the intended environment. | Copilot Studio admin center and Power Platform admin center. |
| Copilot Studio licensing/capacity | The pilot has sufficient messages/capacity and generative AI features if used. | Power Platform admin center capacity and licensing pages. |
| Teams publishing | Teams app publishing is allowed for the pilot audience. | Teams admin center app permission policies, app setup policies, and custom app settings. |
| Power Automate availability | Makers can create solution-aware cloud flows and share run-only user activation where needed. | Power Automate maker portal in the target environment. |
| Outlook connector policy | Office 365 Outlook connector is permitted with delegated `Calendars.Read` and `Mail.Read` style access. | Environment DLP policies and connector details. |
| SharePoint connector policy | SharePoint connector and SharePoint REST calls are permitted. | Environment DLP policies. |
| Approval and Teams notification support | Approvals and private Teams messages/adaptive cards are permitted. | Power Automate connector policy and Teams admin policy. |
| SharePoint site | A site exists for restricted pilot lists and coordinator/admin access. | SharePoint admin center and site permissions. |
| Approved AI classifier | AI Builder GPT/Copilot action/approved internal AI action is callable from scheduled flows. | Environment AI Builder/Copilot Studio availability and DLP policy. |
| Per-mentor flow distribution | A solution package or documented copy-flow process can preserve each mentor's own Outlook connection. | Power Automate solution export/import test with two users. |

## Critical constraints reflected in the design

- Teams sign-in does not grant mailbox access.
- SharePoint enrollment does not grant mailbox access.
- A connector authenticated during an agent conversation is not reused by a central scheduled flow.
- Copilot Studio event triggers are not assumed to run under each enrolled user's mailbox identity.
- Scheduled automated discovery is implemented by a flow each mentor activates with that mentor's own Outlook connection.
- A mentor-owned Outlook connector may request mailbox scopes even if some scan setting is off. For this pilot, automated discovery requires explicit consent to process both calendar and relevant email. Manual submissions remain available without mailbox scanning.
- No tenant-wide application mailbox permissions are used.
- The existing Shadow App is import-only. No API or write-back capability is assumed.
- Scraping is out of scope.

## Smallest viable alternatives for common blockers

| Blocker | Smallest viable alternative |
| --- | --- |
| Copilot Studio Teams publishing is blocked | Run the agent in test mode for makers and use SharePoint/Teams links for pilot users until app approval is granted. |
| AI Builder/GPT action unavailable in scheduled flows | Replace automated classification with deterministic pre-filtering plus mentor manual candidate submission/review. |
| Office 365 Outlook `Mail.Read` disallowed | Disable automated discovery; allow manual mentor submissions and approved imports only. |
| Private Teams notification cards blocked | Use Power Automate approvals and email notifications, after explaining notification content and requiring user confirmation for outbound messages where appropriate. |
| SharePoint unique item permissions restricted | Store private candidates in mentor-private folders/lists or use Dataverse for Teams only if SharePoint cannot enforce mentor-specific access. |

