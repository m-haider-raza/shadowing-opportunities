# Known limitations and unresolved blockers

## Known limitations

- This package cannot verify tenant licensing, capacity, connector DLP policy, Teams app publishing policy, or AI Builder availability from local artifact generation.
- The Power Automate specifications are precise build specs, not claimed platform export files.
- SharePoint is not a transactional database. Capacity protection uses unique keys, list item version checks, and bounded retries. If pilot volume increases or strict transactions are required, reassess the platform choice.
- SharePoint item-level permissions can become operationally complex. If mentor-private candidate isolation cannot be guaranteed, automated discovery must be blocked until a supported private storage alternative is approved.
- Automated discovery quality depends on Outlook connector support for bounded email search and the approved AI classification capability.
- The pilot excludes attachments.
- The pilot does not automatically determine customer/organizer authorization to attend; coordinator review remains required.

## Unresolved blockers to close before real mailbox use

- Confirm Office 365 Outlook delegated calendar and mail scopes are approved for the environment.
- Confirm the exact AI classifier action and data handling policy.
- Confirm Power Automate run history retention and masking/minimization behavior.
- Confirm Teams private notification/adaptive card callback policy.
- Confirm SharePoint list permission model at item scale.
- Confirm approved export provenance and publication authorization rules with ESXP/Shadow App owners.

## Do not proceed to real data until

- Two mentors have activated separate scanner flows with separate Outlook connections in test.
- Private candidate isolation has been demonstrated.
- The AI classifier has passed malicious-instruction tests.
- No private content appears in run history, logs, learner views, or Copilot knowledge sources.

