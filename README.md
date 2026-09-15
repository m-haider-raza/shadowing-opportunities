# Copilot Studio Shadowing Agent pilot

This package is a build-ready implementation guide for an internal low-code pilot that helps Microsoft employees discover and participate in delivery-shadowing opportunities.

It intentionally uses:

- Microsoft Copilot Studio in Teams for the conversational experience.
- Power Automate for scheduled scans, approval workflows, matching, imports, and notifications.
- SharePoint Lists for shared operational records.
- Per-mentor Outlook connections for mailbox-backed automated discovery.

It intentionally does not use custom apps, custom databases, scraping, tenant-wide mailbox application permissions, unsupported Shadow App write-back, or maker-owned mailbox substitution.

## Artifact map

| File | Purpose |
| --- | --- |
| `00-feasibility-validation.md` | Platform checks, verified local capability state, blockers, and smallest alternatives. |
| `01-architecture-and-journeys.md` | Architecture and user-journey diagrams. |
| `02-sharepoint-schema-and-permissions.md` | Logical model, list design, columns, permissions, retention, and privacy boundaries. |
| `03-copilot-studio-agent.md` | Agent instructions, topics, tools/actions, auth rules, and conversation examples. |
| `04-power-automate-workflows.md` | Flow designs for mentor scanning, review, request, coordinator, matching, lifecycle, and health. |
| `05-ai-classification.md` | Classification prompt, structured JSON schema, validation rules, and correlation rules. |
| `06-import-workflow.md` | Approved export ingestion, configurable mapping, preview, idempotency, and duplicate handling. |
| `07-deployment-and-configuration.md` | Tenant placeholders, deployment steps, per-mentor activation guide, and operations. |
| `08-known-limitations-and-blockers.md` | Known limitations, assumptions, unresolved blockers, and alternatives. |
| `sharepoint/list-schemas.json` | Machine-readable SharePoint list schema specification. |
| `flow-specs/*.json` | Power Automate flow specifications. These are not claimed to be importable platform exports. |
| `copilot-studio/topics.yaml` | Topic/tool design for Copilot Studio implementation. This is a build spec, not a fabricated export. |
| `demo-data/*.csv` | Synthetic demonstration data. |
| `tests/acceptance-tests.md` | Acceptance test suite mapped to the requirements. |

## First end-to-end slice

Build and validate in this order:

1. Create SharePoint lists and Entra security groups.
2. Build the Copilot Studio agent topics and Teams publishing package.
3. Build the per-mentor scheduled scanner flow using a mentor-owned Outlook connection.
4. Build private candidate review and exact-text publication.
5. Build learner search, request submission, mentor approval, and coordinator invitation queue.
6. Run synthetic acceptance tests before touching real mailbox data.

Automated discovery must not run until the mentor has explicitly activated the per-mentor flow and authorized both calendar and relevant-email processing.

