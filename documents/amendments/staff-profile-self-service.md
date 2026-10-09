# Staff profile ownership and self-service — issue #36

Decision date: 9 October 2026. Policy selected by Tasheen during implementation.
This records the agreed implementation change for review; it does not represent
client or supervisor sign-off. Incorporation into revised SRS/SDS editions and
their formal approval remain pending.

## Source and clarification

The SRS section 3.4.1 describes user names and section 3.4.6 describes account
and profile relationships. SDS Figure 3 (PDF page 15) already defines STAFF
with full_name, designation and a USER relationship; the normalized schema is
on PDF pages 24 and 30. Sources:

- [SRS](../srs/Hostel%20Hive%20SRS%20(1).pdf)
- [SDS](../sds/HostelHive%20SDS%20(4).pdf)
- [ADR-002](../adr/ADR-002-firebase-authentication.md)

Staff self-service is an explicit policy extension, rather than a claim that
the original STAFF diagram specified these permissions.

## SRS requirement amendment

An authenticated, active staff account (Admin, Warden, Sub-Warden or Security
Staff) may create its own staff profile and update its own full name. The user
account must already exist; this does not provide public staff registration.

Admins may list, view, create and update staff profiles for other staff accounts,
including their designations. Staff cannot use self-service to change designation,
email, role, account status or another person's profile. Existing account
management permissions continue to control role and status changes. Students
use the student module and cannot create staff profiles.

A self-created profile may have an unassigned designation until an Admin enters
one. Designation is descriptive and grants no permissions. Staff can read their
current account and profile through `/api/v1/me`. Deactivation blocks self-service
and preserves profile data. Role changes preserve historical profiles while
account reads project only the current role's profile.

## SDS design amendment

Keep one Go backend with separate identity, staff and student modules:

| Module | Owned data and responsibility |
| --- | --- |
| identity | Common users accounts, Firebase identity mapping, email, roles and status |
| staff | Staff name and designation, linked to users through unique user_id |
| student | Student and guardian details, images and student-specific workflows |

These modules share one PostgreSQL database. The account_profiles view derives
current-role names without creating another editable copy of profile fields.
There is no separate module/table for each staff role and no new service or repo.
Firebase remains the credential authority under ADR-002.

PUT `/api/v1/staff/me` accepts only full_name, derives ownership from the verified
Firebase UID, and rechecks active staff status inside the database transaction.
Admin PUT `/api/v1/staff/{userID}` requires both full_name and designation.
Self updates preserve existing designation even when concurrent with Admin writes.

Migration 8 creates staff_profiles and account_profiles. Migration 9 adds an
empty designation default for self-created profiles and permits that state.
Admin input still requires a nonblank designation. Rolling back migration 9
requires all incomplete designations to be assigned first; it does not discard
profiles or fabricate designation values.

## Verification and remaining approval

Automated checks cover all staff roles, denied Student/inactive access, ownership,
rejected privileged fields, designation preservation, concurrent Admin/self writes,
profile identity/timestamps and migration rollback/reapply. Record actual live
results and teammate review in issue #36 before marking it complete. This change
does not resolve the separate student CSV import or shared release-validation work.
