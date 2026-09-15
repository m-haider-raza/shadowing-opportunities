# Power Automate workflows

Flow specifications are also provided in `flow-specs/*.json`. They are build specifications, not fabricated importable Power Automate exports.

## 1. Per-mentor scheduled scanner flow

Owner: each mentor.

Connections: mentor's own Office 365 Outlook connection, SharePoint, approved AI classifier, Teams/Approvals if permitted.

Default schedule:

- Daily in mentor time zone.
- Look ahead 14 days.
- Send one digest only when new or materially changed candidates exist.
- Do not send empty digests.

High-level steps:

1. Trigger on recurrence.
2. Read `ScannerConfigurations` by authenticated flow owner mapping.
3. Stop with visible health state if mentor is not enrolled, scanning is paused, flow owner mismatch is detected, or calendar/email consent is absent.
4. Read calendar view for the configured window using the mentor's Outlook connection.
5. Exclude cancelled, private/sensitive, personal, HR/legal/performance, and clearly inappropriate events before AI classification.
6. For each remaining event, compute a fingerprint from minimized fields and source identifiers.
7. Skip unchanged fingerprints.
8. Search bounded relevant email context:
   - Meeting/invitation identifiers when available.
   - Subject/project terms plus participant overlap and timing window.
   - Email threads that arrange a delivery and map to an upcoming meeting.
9. Require corroborating evidence. Shared participant, generic customer name, or generic subject alone is insufficient.
10. If email access fails, mark run incomplete and do not present calendar-only scan as complete.
11. If no relevant email is found, create a private lead requiring mentor input; do not auto-publish.
12. Send minimized eligible evidence to approved AI classifier.
13. Validate JSON schema and risk flags.
14. Store private candidate summary, proposed listing JSON, source hashes, review version, disposition, retention date.
15. Reconcile previously published listings for cancellation or material changes:
    - Cancellation: withdraw listing and notify impacted participants through approved workflow.
    - Material change: set `NeedsReReview`; do not silently overwrite approved content.
16. Update scanner health without logging raw private content.

## 2. Candidate publication flow

Trigger: Copilot Studio action or adaptive card from authenticated mentor.

Rules:

- Verify current user is the candidate owner or authorized coordinator/admin.
- Verify candidate `Disposition` is New/InReview and version matches the approved version.
- Publish exactly the approved proposed learner-visible listing.
- Store `ApprovedCandidateId` and `ApprovedReviewVersion`.
- Do not regenerate or materially alter title/description after approval.
- Audit actor, candidate ID, version, and outcome.

## 3. Learner request flow

Trigger: Copilot Studio action from authenticated learner.

Concurrency-safe logic:

1. Compute `ActiveRequestKey = LearnerUserObjectId + "|" + OpportunityPublicId`.
2. Attempt to create request with unique `ActiveRequestKey`.
3. If duplicate active request exists, return existing status.
4. Read opportunity with version/ETag.
5. If status is not Published or `ReservedSeats >= Capacity`, reject without creating a reservation.
6. Update `ReservedSeats = ReservedSeats + 1` using SharePoint version/ETag or REST `IF-MATCH`.
7. If update conflict occurs, retry a limited number of times, then return capacity conflict.
8. Set request `Status=PendingMentor` and `PlaceReserved=true`.
9. Send mentor approval card containing minimized learner objective.

Reserved states:

- `PendingMentor`
- `MentorApproved`
- `CoordinatorQueued`
- `InvitationArranged`

Release capacity when status becomes:

- `MentorDeclined`
- `Withdrawn`
- `Cancelled`
- `Expired`
- `InvitationNotAuthorized`

## 4. Mentor request approval flow

Trigger: Approval/adaptive card callback.

Rules:

- Verify callback actor is the opportunity mentor or authorized coordinator/admin.
- Verify request is still `PendingMentor`.
- Use callback nonce/version to prevent repeated processing.
- Mentor approval sets `Status=MentorApproved`, then creates coordinator task and sets `CoordinatorQueued`.
- Mentor decline releases capacity and sets `MentorDeclined`.
- No meeting invitation is sent or forwarded.

## 5. Coordinator invitation task flow

Trigger: request enters `CoordinatorQueued`.

Coordinator actions:

- Check organizer/customer/CSAM attendance permissions outside the agent.
- Arrange invitation with an authorized organizer.
- Record outcome:
  - `InvitationArranged`
  - `InvitationNotAuthorized`
  - `Cancelled`
  - `NeedsMoreInfo`

The flow never exposes join links to learners and never automatically adds attendees.

## 6. Learner matching digest

Trigger: scheduled coordinator/admin-owned flow.

Rules:

- Only include published eligible opportunities.
- Respect learner enrollment, notification preferences, access group, language, topic, level, time zone, and time-window preferences.
- Deduplicate notifications by learner/opportunity digest history.
- Do not include private candidate rationale or source references.

## 7. Scanner health and lifecycle flow

Capabilities:

- Pause/resume scanning.
- Revoke scanner config after mentor disconnects flow or withdraws enrollment.
- Expire old listings.
- Delete old private candidates by retention date.
- Surface connection failures to mentor and coordinator.
- Produce coordinator view of outstanding approvals, stale listings, import errors, and scanner health.

No raw mailbox content is stored in operational logs or run history.

