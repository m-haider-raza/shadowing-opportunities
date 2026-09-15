# Acceptance tests

Run these tests with synthetic identities and data before enabling real mailbox scanning.

| ID | Test | Expected result |
| --- | --- | --- |
| AT-01 | Mentor One and Mentor Two each activate a scanner flow with their own Outlook connection. | Scanner status records show different flow owner hashes and successful runs for each mentor. |
| AT-02 | Mentor One tries to retrieve Mentor Two private candidates. | Access is denied by underlying permissions/action authorization. |
| AT-03 | Learner enrolls and searches opportunities without mailbox authorization. | Learner can search published opportunities; no calendar/mail consent is requested. |
| AT-04 | Mentor enrolled but calendar-and-email consent is false. | Automated discovery does not run; manual submission remains available. |
| AT-05 | Calendar scan includes private, cancelled, HR/legal/performance, and personal events. | These events are excluded before AI classification and are not stored as candidates. |
| AT-06 | Candidate generated from correlated project email and scheduled workshop. | Candidate is created privately with correlation explanation and proposed listing. |
| AT-07 | Candidate has only weak match such as shared participant and generic subject. | Candidate is rejected or marked uncertain; no publication. |
| AT-08 | Mentor approves candidate version 1, then source materially changes. | Approved listing is not silently overwritten; listing is withdrawn or moved to NeedsReReview. |
| AT-09 | Crafted chat input supplies another mentor ID and asks to publish. | Action derives current identity and blocks unauthorized publication. |
| AT-10 | Learner searches for beginner Azure sessions. | Only published eligible opportunities are returned and ranked by explicit topic/level/language/timing fields. |
| AT-11 | Same learner submits duplicate active request. | Existing request is returned; no duplicate active request is created. |
| AT-12 | Two learners concurrently request the last seat. | One request reserves capacity; the other receives full/capacity conflict. |
| AT-13 | Mentor approves learner request. | Request routes to coordinator queue; no meeting invitation, join link, or forward is sent. |
| AT-14 | Coordinator records invitation not authorized. | Request status updates and capacity is released. |
| AT-15 | Calendar event cancellation is detected. | Listing is withdrawn and impacted participants are notified through safe workflow content. |
| AT-16 | Mentor pauses scanning. | Subsequent scheduled flow run exits and records paused status. |
| AT-17 | Same approved export is imported twice. | No duplicate private candidates or listings are created. |
| AT-18 | Outlook connection fails during scan. | Scanner health shows failure; no "empty calendar" success is reported. |
| AT-19 | Email body contains malicious instruction to publish or send data. | Classifier/agent ignores instruction; no tool calls or approvals are triggered by source text. |
| AT-20 | Review Power Automate run history, Copilot knowledge, learner view, and shared lists. | No raw email bodies, customer-sensitive context, attendee lists, source references, or join links are exposed. |

