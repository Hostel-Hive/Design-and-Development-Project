# Attendance scan contract — backend #55

Tasheen approved explicit direction, configurable 10-second suppression of
repeated same-direction scans, and online-only processing on 11 October 2026.
This records the implementation decision; it does not claim supervisor sign-off
or completion of controlled SRS/SDS PDF revisions.

## Requirements and design amendment

- FR011–FR014: authenticated scanners submit generated HostelHive `HH1:` QR
  identifiers. Admin assigns each scanner `IN` or `OUT`. The backend reads this
  direction from the scanner record; a scan cannot supply its own direction.
- Replace FR013's automatic IN/OUT toggle with explicit movement. Repeated
  entry never turns IN into OUT. Two readers, or a supervised deployment that
  deliberately configures direction, are required. This backend ticket does not
  implement a firmware direction-selection UI.
- FR012: PostgreSQL assigns UTC timestamps after authorization and student locks.
  Client timestamps, delayed batches and offline capture are not accepted.
- FR014: suppress a new event for the same student and direction for 10 seconds
  after the last recorded movement in that direction, across devices. Configure
  `ATTENDANCE_COOLDOWN` as a positive duration no greater than five minutes.
  Opposite-direction scans are not blocked by that cooldown. Hardware testing
  must validate the starting value. A quick IN→OUT→IN within the entry cooldown
  still suppresses the second IN under this rule.
- Every scan has a device-scoped UUID event ID. Same ID and QR return the original
  committed result; different QR with the same ID is a conflict. IDs are retained
  with history and must not be reused. Retrying a known event never updates
  current presence, including after scanner direction or student status changes.
  Disabled scanners and invalidated credentials cannot retrieve that result.
- Unknown QR, archived student profile, inactive account or non-student role
  cannot create new events. Device active status and credential version are
  checked transactionally. Simultaneous scans serialize on the student row.
- Presence initially means UNKNOWN. The first accepted IN or OUT establishes
  the recorded state. A scan is a declared movement, not proof of passage.
- Admin/Warden can read a student's presence and event history. History includes
  recorded movements and ignored cooldown/already-IN/OUT outcomes. Historical
  records remain readable after archival or account deactivation; their presence
  must be interpreted alongside current account and profile eligibility.

## Scope remaining

Firmware feedback must distinguish recorded, ignored and network-failure results.
No success indication is allowed without server confirmation. Staff need a
manual outage procedure. Offline storage/synchronization, passage sensors,
manual presence corrections, realtime RTDB publication, aggregates, student
self attendance APIs, leave interactions (D07), analytics and report exports
remain separate work. Existing university-card compatibility and dynamic QR
copy prevention are not introduced; D11 source-wording review remains pending.

## SDS additions

Add attendance_events (device/event primary key, student reference, payload hash,
direction, outcome and UTC timestamp), student_presence (last accepted movement),
scanner direction and atomic direction-configuration audit. QR tokens and device
credentials are excluded from event responses and attendance history. Reuse the
existing attendance module layers; PostgreSQL remains authoritative under ADR-004.

Approval evidence: user's explicit request, “These decisions are correct. Let's
implement,” and supplied backend issue #55. Review significant deviations within
one sprint per the existing decision-register procedure.
