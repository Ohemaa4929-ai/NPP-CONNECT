# NPP CONNECT: Connecting Leadership to the Grassroots

Organizational communication platform: broadcasts from leaders to audiences by hierarchy, plus chat, groups and confidential groups.
Party colors: red, white, blue. Demo mode uses fictional accounts only.

    npp-connect/
      mobile/     Expo (React Native, TypeScript) app, demo mode on by default
      admin/      Next.js admin dashboard (2FA required)
      supabase/   Postgres migrations 0001-0005, seed.sql, pgTAP tests, Edge Function (broadcast fan-out)
      .github/    CI: typecheck, unit tests, database tests

## Status: written, not yet run
Nothing here has been executed. The build environment had no network access, so no install, type check, test run or database run has happened.
Expect compile errors and policy bugs on first run. Fix them on DEV before anything else.

## First run (local)
1. `cd supabase && supabase init && supabase start`, copy `config.toml.example` settings, then `supabase db reset` (applies migrations and seed.sql).
2. `psql ... -c "alter database postgres set app.require_admin_mfa = 'off'"` for local dev only. Never staging or production.
3. `cd mobile && cp .env.example .env && npm install && npx expo install --fix && npx expo start`. Demo mode needs no backend.
4. For the live backend, set `EXPO_PUBLIC_DEMO_MODE=false` and the Supabase URL and anon key. Test numbers +233200000001 to 06, code 123456.
5. `cd admin && cp .env.example .env.local && npm install && npm run dev`. Admin sign-in uses email, password and an authenticator app.
6. `supabase test db` runs the access-control tests. Deploy the Edge Function: `supabase functions deploy broadcast-fanout`, then schedule it every minute.

## What is built
Auth (phone OTP, +233), profiles with privacy, configurable hierarchy, role and permission tables, per-unit broadcast grants, broadcasts (draft, schedule, cancel, audience by area, attachments, poll, comments, reactions, confidential, queue-based fan-out, read tracking), 1:1 and group chat (text, image, video, voice, document, reply, forward, react, pin, delete, report, in-chat search, cursor pagination, realtime per conversation, offline outbox with idempotent retry), confidential groups (invitation only, no forwarding, disappearing messages, member removal audited), leadership videos, events with registration and reminders, polls (single, multiple, anonymous, closing date), global search with filters, notifications and push, privacy and notification settings, device list and revoke, moderation and reports, audit logs, rate limits, private storage buckets with signed URLs, admin dashboard (stats, users, hierarchy, broadcasts, moderation, audit, role matrix), admin 2FA and idle timeout.

## Known gaps (not done, listed so nothing is assumed)
- No end-to-end encryption, and the app must not claim any. Confidential chats are access-controlled only.
- Malware scanning: `files.scan_status` exists and blocked files are unreadable, but no scanner is connected.
- Delivery state: messages show Sent and Read (read receipts). Per-device "delivered" acknowledgements are not implemented.
- Push is sent for broadcasts only. Chat, mention, event-reminder and poll pushes need more Edge Function code.
- Server-side video transcoding and thumbnails: compression happens on the device only. Video cards use a plain play button.
- No UI yet for profile photo or group photo upload, @mentions, message editing, or hard-deleting a user for a data request (soft removal only).
- Admin dashboard has no separate Videos, Files or Events pages. Create events and videos through SQL or add admin pages.
- Event and video creation screens are not in the mobile app (reading, registering, saving and reporting are).
- Load, security, backup and disaster-recovery tests (Phase 6) have not been done. Expect to tune indexes after load tests.
- Terms, Privacy Policy and Ghana data-protection compliance need legal review. Political opinion data is sensitive: minimize and retain carefully.
- Package versions in `package.json` are best guesses. `npx expo install --fix` aligns them.
