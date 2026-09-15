# SharePoint schema and permission design

## Lists

| List | Why separate |
| --- | --- |
| ParticipantProfiles | Shared profile/preferences with role-specific fields and user identity binding. |
| ScannerConfigurations | Restricted scanner activation, settings, health, consent, and connection status. |
| PrivateCandidates | Mentor-private AI-derived drafts and source references; excluded from agent knowledge sources and learner access. |
| PublishedOpportunities | Learner-visible approved listings with safe minimized fields only. |
| ShadowRequests | Request lifecycle, mentor decision, capacity reservation, coordinator outcome. |
| ImportBatches | Coordinator import mapping, provenance, preview, errors, and batch state. |
| AuditHistory | Append-only operational/audit events with actor, action, timestamp, entity, version. |

## Permission groups

Use Entra security groups or Microsoft 365 groups:

| Group | Purpose |
| --- | --- |
| `${PILOT_LEARNERS_GROUP}` | Learners who may use the agent and read published opportunities. |
| `${PILOT_MENTORS_GROUP}` | Mentors who may enroll and own listings. |
| `${PILOT_COORDINATORS_GROUP}` | Coordinators who manage imports and invitation tasks. |
| `${PILOT_ADMINS_GROUP}` | Administrators who configure lists, flows, and retention. |

## Permission rules

- ParticipantProfiles: read/write own item through flows; coordinators/admins read for operations; learners do not receive mentor-private scanner fields.
- ScannerConfigurations: mentor can read/update own config through flow or agent action; coordinators/admins can read scanner health; no learner access.
- PrivateCandidates: no learner access; not configured as Copilot Studio knowledge. Each item breaks inheritance or is stored in mentor-specific restricted storage granting only the owning mentor, coordinators, and admins.
- PublishedOpportunities: read by pilot learners/mentors/coordinators; write only through publication flow after exact-text mentor approval.
- ShadowRequests: learner can read own requests; mentor can read requests for own opportunities; coordinators/admins can read operational queue. Writes occur through validated flows.
- ImportBatches: coordinators/admins only.
- AuditHistory: admins/coordinators read; append-only writes through flows.

If SharePoint item-level permissions cannot be reliably enforced for PrivateCandidates at pilot scale, use separate mentor-private lists/folders or stop automated discovery. Do not rely on prompt filtering or SharePoint views as security boundaries.

## Minimal columns

The machine-readable column plan is in `sharepoint/list-schemas.json`.

### ParticipantProfiles

- `UserObjectId` text, required, unique
- `UserPrincipalName` text, required
- `DisplayName` text
- `Roles` choice multi: Learner, Mentor, Coordinator, Administrator
- `Topics` choice/text multi
- `DeliveryTypes` choice multi
- `Languages` choice multi
- `TimeZone` text
- `ExperienceLevel` choice
- `LearningGoals` note
- `DefaultShadowCapacity` number
- `NotificationPreference` choice
- `RecommendationPaused` yes/no
- `MentorEnrollmentStatus` choice: NotEnrolled, Enrolled, Withdrawn
- `LearnerEnrollmentStatus` choice: NotEnrolled, Enrolled, Paused

### ScannerConfigurations

- `MentorUserObjectId` text, required, unique
- `ScanningEnabled` yes/no
- `CalendarAndEmailConsent` yes/no
- `FlowActivationStatus` choice: NotActivated, Activated, Failed, Paused, Revoked
- `RunFrequency` choice
- `LookAheadDays` number
- `LastSuccessfulRunUtc` date
- `LastAttemptUtc` date
- `LastErrorCategory` choice
- `LastErrorSummary` text
- `ConnectionOwnerUpnHash` text
- `RetentionDaysForCandidates` number

### PrivateCandidates

- `MentorUserObjectId` text, required
- `SourceType` choice: CalendarEmailCorrelation, ManualSubmission, ApprovedExport
- `SourceReferenceHash` text, indexed
- `EventIdHash` text
- `EventFingerprint` text
- `CorrelationEvidenceSummary` note
- `PrivateReviewSummary` note
- `ProposedListingJson` note
- `ReviewVersion` number
- `Disposition` choice: New, InReview, Approved, Edited, Rejected, Superseded, Expired
- `RiskFlags` choice multi
- `RetentionDeleteAfterUtc` date

### PublishedOpportunities

- `OpportunityPublicId` text, required, unique
- `MentorUserObjectId` text, required
- `ApprovedCandidateId` lookup
- `ApprovedReviewVersion` number
- `Title` text
- `Description` note
- `Topic` choice/text
- `DeliveryType` choice
- `StartUtc` date
- `EndUtc` date
- `TimeZone` text
- `Language` choice
- `Level` choice
- `Prerequisites` note
- `Capacity` number
- `ReservedSeats` number
- `Status` choice: Published, Full, Withdrawn, Expired, NeedsReReview
- `Provenance` choice: MentorScan, Manual, Import

### ShadowRequests

- `RequestPublicId` text, required, unique
- `ActiveRequestKey` text, unique when active
- `OpportunityPublicId` lookup/text
- `LearnerUserObjectId` text
- `MentorUserObjectId` text
- `LearnerObjective` note
- `Status` choice: PendingMentor, MentorDeclined, MentorApproved, CoordinatorQueued, InvitationArranged, InvitationNotAuthorized, Withdrawn, Cancelled, Expired
- `PlaceReserved` yes/no
- `MentorDecisionUtc` date
- `CoordinatorOutcome` choice
- `CoordinatorNotes` note

### ImportBatches

- `BatchId` text, required, unique
- `SourceName` text
- `SourceFileName` text
- `SourceSnapshotType` choice: CompleteSnapshot, PartialExport, Unknown
- `MappingJson` note
- `PreviewJson` note
- `Status` choice: Uploaded, Previewed, Confirmed, Processed, Failed
- `ErrorSummary` note
- `RowsProcessed` number
- `RowsFailed` number

### AuditHistory

- `ActorUserObjectId` text
- `ActorRoleAtAction` text
- `Action` text
- `EntityType` text
- `EntityId` text
- `EntityVersion` text
- `TimestampUtc` date
- `Outcome` choice: Success, Failure, Blocked
- `DetailsMinimized` note

## Retention

| Data | Default pilot retention |
| --- | --- |
| Private candidates not approved | 30 days after creation or rejection. |
| Superseded candidate versions | 30 days after supersession. |
| Published opportunities | 180 days after event end, or shorter if withdrawn by mentor. |
| Requests | 180 days after final status. |
| Import batches | 90 days for previews/errors; provenance summaries retained with created records. |
| Audit history | 1 year unless pilot policy requires shorter/longer. |

Power Automate run history must not contain raw email bodies, private notes, join links, or customer-sensitive text. Store minimized summaries and hashes only.

