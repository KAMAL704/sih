# BIDGUARD AI — Flutter + Supabase

An SIH-ready, officer-led GeM bid compliance verification app. It presents a working demo flow:

`document → AI extraction → sandbox verification → cross-verification → compliance → risk → recommendation → officer decision → audit`

## Important prototype boundary

This app uses **deterministic demo data and sandbox interfaces**. It does not access, claim access to, or simulate authorization for restricted Government systems. AI recommendations are advisory only. Final qualification/disqualification always rests with the Procurement Officer.

## What is included

- Flutter Material 3 dashboard, tender management, bidder digital twin and evidence-led compliance view
- Interactive staged “Run AI verification” workflow
- Explainable recommendation, risk reasons, officer-decision control, audit events and report view
- Demo credentials and Admin-only integration toggles
- Supabase-ready PostgreSQL schema, Storage buckets, RLS starting policies and Edge Function interface
- Offline-first demo behavior — all core UI works immediately without configuring Supabase

## Run the app

The workspace environment could not update Flutter’s shared SDK cache, so platform folders are intentionally not generated here. On a local Flutter installation:

```bash
cd bidguard-flutter
flutter create .
flutter pub get
flutter run -d chrome
```

For a configured Supabase environment:

```bash
flutter run -d chrome \
  --dart-define=SUPABASE_URL=https://YOUR-PROJECT.supabase.co \
  --dart-define=SUPABASE_ANON_KEY=YOUR_ANON_KEY
```

Do not put a Supabase service-role key in the Flutter app.

## Demo logins

| Role | Email | Password |
|---|---|---|
| Procurement Officer | `officer@gov.in` | `demo123` |
| Admin | `admin@gov.in` | `demo123` |

## Supabase setup

1. Create a Supabase project.
2. In the SQL Editor, run [`supabase/schema.sql`](supabase/schema.sql).
3. Create real Auth users and matching `profiles` records with `procurement_officer` or `admin` roles.
4. Deploy the mock verification boundary:

```bash
supabase functions deploy run-verification
```

5. Replace the temporary prototype RLS policies with role-specific policies before deploying outside a demo.

The schema includes: profiles, tenders, tender requirements, bidders, bids, documents, extracted fields, verifications, compliance checks/scores, risk assessments, discrepancies, AI recommendations, officer decisions, audit logs and integrations.

## Supabase architecture

| Concern | Supabase service |
|---|---|
| Login and roles | Auth + `profiles` |
| Procurement records | Postgres tables with RLS |
| Tender/bidder files | Private Storage buckets |
| OCR + document classification | Edge Function calling an approved provider |
| Government adapters | Edge Functions with authorized server-side credentials |
| Rule/risk calculation | Edge Functions / Postgres RPC |
| Auditability | `audit_logs` written for each material action |

## Production hardening

- Implement role-specific RLS for every table (the supplied policy is intentionally only a prototype baseline).
- Use Storage access policies, file type/size checks and malware scanning.
- Keep government credentials and LLM keys only in Edge Function secrets.
- Never send restricted data or service-role keys to the Flutter client.
- Preserve audit entries as append-only and build document retention/access controls.
- Treat unavailable or uncertain verification results as manual-review cases.

## Demo flow

1. Sign in as the Procurement Officer.
2. Open **Tenders** → `GEM/2026/001` → **Requirements**.
3. Open **Bidders**, select `XYZ Industries Ltd` or `PQR Enterprises`.
4. Open **Verification** and click **Run AI verification**.
5. Review evidence and explainable findings; choose a decision and add remarks.
6. Open **Audit Trail** and **Compliance Reports** for the record.
# sih
