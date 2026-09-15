# Approved-export ingestion workflow

## Scope

Coordinator-led import supports authorized CSV or Excel exports from ESXP/the existing Shadow App where available.

The pilot does not assume:

- A fixed export schema.
- A verified ESXP/ROSS API.
- A source refresh cadence.
- Write-back capability.
- That permission to export implies permission to republish.

## Flow

1. Coordinator uploads CSV/XLSX to a restricted SharePoint document location.
2. Import flow reads headers and samples rows.
3. Coordinator maps source fields to pilot fields.
4. Flow validates required target fields:
   - source record identifier or stable row fingerprint
   - source name
   - title or description candidate
   - date/time if the record is scheduled
   - responsible owner or explicit owner-missing flag
   - publication authorization status or explicit review-needed flag
5. Flow creates preview with row-level outcomes.
6. Coordinator explicitly confirms processing.
7. Flow creates private candidates or import review records, not published opportunities.
8. Flow records batch provenance, row errors, source freshness, and snapshot type.

## Idempotency and duplicates

- Use `SourceName + SourceRecordId` when available.
- Otherwise use a stable normalized fingerprint from source title/date/owner/provenance.
- Re-import updates the existing import candidate when source ID/fingerprint matches.
- Missing rows in a later export do not imply cancellation unless the source is explicitly marked as a complete snapshot.
- Calendar-derived and import-derived overlap detection is advisory. Ambiguous overlaps are not merged automatically.

## Review path

Imported records remain unpublished until:

- A responsible owner is identified.
- Disclosure/eligibility review is complete.
- Exact learner-visible listing text is approved by an authorized mentor/coordinator path.

Records missing owner or publication authorization go to the coordinator exception queue.

