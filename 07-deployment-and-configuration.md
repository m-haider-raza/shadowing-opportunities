# Deployment and configuration

## Placeholders

Replace these values before deployment:

| Placeholder | Meaning |
| --- | --- |
| `${TENANT_ID}` | Microsoft tenant ID. |
| `${POWER_PLATFORM_ENVIRONMENT}` | Target Power Platform environment. |
| `${SHAREPOINT_SITE_URL}` | Restricted SharePoint site URL. |
| `${PILOT_LEARNERS_GROUP}` | Learner security group. |
| `${PILOT_MENTORS_GROUP}` | Mentor security group. |
| `${PILOT_COORDINATORS_GROUP}` | Coordinator security group. |
| `${PILOT_ADMINS_GROUP}` | Administrator security group. |
| `${COORDINATOR_UPN}` | Coordinator identity or group mailbox used for operational ownership. |
| `${RETENTION_PRIVATE_CANDIDATE_DAYS}` | Default 30. |
| `${RETENTION_REQUEST_DAYS}` | Default 180. |

## Build steps

1. Confirm feasibility checks in `00-feasibility-validation.md`.
2. Create the SharePoint site and groups.
3. Create lists using `sharepoint/list-schemas.json`.
4. Configure permissions and item-level/private candidate enforcement.
5. Build Copilot Studio agent topics from `copilot-studio/topics.yaml`.
6. Create Power Automate flows from `flow-specs/*.json` and `04-power-automate-workflows.md`.
7. Configure DLP-approved connectors:
   - SharePoint
   - Office 365 Outlook
   - Approvals
   - Microsoft Teams
   - approved AI classification action
8. Publish the Copilot Studio agent to Teams for a pilot security group only.
9. Load `demo-data/*.csv` into the lists for synthetic testing.
10. Run `tests/acceptance-tests.md`.

## Per-mentor scanner activation guide

Give each mentor these steps:

1. Open the mentor scanner flow template link provided by the pilot admin.
2. Save a personal copy or activate the shared template according to the environment's supported flow distribution method.
3. Authenticate the Office 365 Outlook connection with your own account.
4. Authenticate SharePoint and Teams/Approvals if prompted.
5. Confirm the trigger is enabled.
6. In the Shadowing Agent, select "Verify scanner activation."
7. The agent should show `FlowActivationStatus=Activated` only after a successful heartbeat/run writes scanner status for your identity.

Important: being enrolled as a mentor is not the same as having a working scanner. Automated discovery starts only after both calendar-and-email consent and successful mentor-owned flow activation are present.

## Operations

Coordinator view must show:

- Pending candidate reviews.
- Mentor-approved requests awaiting attendance permission checks.
- Import errors.
- Stale listings.
- Scanner failures.
- Paused/revoked scanners.
- Listings affected by meeting cancellation or material change.

Administrators should review retention deletion runs and DLP changes monthly during the pilot.

