# HostelHive - Design and Development Project

HostelHive digitizes student attendance, room and bed allocation, leave requests, complaints, announcements and reporting for Bulugaha Girls' Hostel at the University of Kelaniya.

This repository holds design documents and development planning artefacts. The
application source lives in the [backend](https://github.com/Hostel-Hive/hostelhive-backend)
and [frontend](https://github.com/Hostel-Hive/hostelhive-frontend) repositories.

## Live assessment application

| Service | URL |
| --- | --- |
| Student portal | [Student login](https://web-kohl-delta-90.vercel.app/login) |
| Admin and Warden portal | [Staff login](https://web-kohl-delta-90.vercel.app/staff/login) |
| Backend | [API base](https://hostelhive-backend-dev.onrender.com/api/v1) |
| API documentation | [Swagger UI](https://hostelhive-backend-dev.onrender.com/docs/) |
| API specification | [OpenAPI JSON](https://hostelhive-backend-dev.onrender.com/openapi.json) |
| Service checks | [Health](https://hostelhive-backend-dev.onrender.com/health), [database readiness](https://hostelhive-backend-dev.onrender.com/ready) |

The shared assessment environment deploys `develop`: Vercel hosts the frontend,
Render hosts the backend and Supabase hosts PostgreSQL. Fresh-machine setup,
required environment variables, CI badges and coverage-report links are in the
[backend README](https://github.com/Hostel-Hive/hostelhive-backend/blob/develop/README.md)
and [frontend README](https://github.com/Hostel-Hive/hostelhive-frontend/blob/develop/README.md).

### Demo accounts

Shared assessment accounts use test data. Admin and Warden sign in through the
staff portal; Student signs in through the student portal.

| Role | Email | Password |
| --- | --- | --- |
| Admin | `darshikatasheen99@gmail.com` | `JackCM@3003` |
| Warden | `navonsanjuni@gmail.com` | `Sanjuni@1234` |
| Student | `kavindithagya21@gmail.com` | `Thagya21` |
