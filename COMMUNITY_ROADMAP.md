# Durga Map — Community Map Roadmap

## Goal

Turn Durga Map from a personal GPS-photo explorer into a shared, community-contributed map of Durga Puja pandals, while preserving the working Android photo GPS workflow.

## Product principles

- Keep the existing Android GPS extraction and map working.
- A contribution creates a **pandal/place record**, not just another photo pin.
- Never upload a contributor's original photo automatically. Uploading a photo must be an explicit action.
- Store only the location and information needed to discover a pandal; do not expose a contributor's private profile or precise home location.
- Community submissions are pending review before becoming public.
- Keep a clear source/status for each place: community submitted, verified, or needs correction.
- Make the web app and Android app use the same shared place data.

## MVP data model

### Pandals / places
- ID
- Public name
- Latitude and longitude
- Address / area (optional)
- Puja year
- Short description (optional)
- Submitted by (account ID; never displayed publicly by default)
- Status: pending, published, rejected, or needs correction
- Created/updated timestamps

### Contributions / reports
- Place ID
- Contributor account ID
- Type: correction, update, duplicate report, or safety/moderation report
- Suggested change and optional note
- Review status and timestamps

### Photos (later milestone)
- Place ID
- Storage path and caption
- Uploader account ID
- Explicit consent and moderation status
- No public image upload until access rules and storage limits are defined

## Proposed implementation milestones

1. **Foundation and safety rules** — document the MVP, data model, moderation, and privacy rules. (This document.)
2. **Dedicated backend** — set up a separate Supabase project for Durga Map (do not reuse the Money Calculator database). Confirm cost/plan before creating anything billable.
3. **Database and access control** — initial `pandals` and `pandal_submissions` tables are live with RLS. Public visitors can read published pandals; new submissions remain pending and are not readable publicly. Database triggers block submissions within 30 metres of another pandal and matching normalized names within 2 km.
4. **Contribution flow** — first version committed to `index.html` and `durga-puja-map.html`: users can submit a pandal name, district/area, optional address/details, and map coordinates; submissions remain pending review. The app reads published pandals from Supabase and displays shared markers.
5. **Moderation workflow** — next: create a safe moderator-only approval/rejection workflow. Until then, submissions require manual review in the Supabase dashboard; do not expose approval actions to the public client.
6. **Shared map** — load published places from the backend; add search and filters for area and year.
7. **Accounts and abuse controls** — add sign-in before public submission, rate limits, basic anti-spam controls, and moderator permissions.
8. **Optional photo contributions** — only after consent, storage rules, and moderation are implemented.
9. **Verification** — test on Android and web, test access rules with signed-out and signed-in users, and confirm private/pending data is not exposed.

## Architecture direction

The current project is a static HTML/Leaflet app wrapped with Capacitor for Android. A managed backend is needed for a genuinely shared map. Supabase is the proposed option because it can provide PostgreSQL, authentication, and storage without replacing the current frontend. Use a **separate Durga Map project**, least-privilege row-level security, and a public/publish workflow. The initial frontend-to-Supabase connection is committed. Remaining work includes testing the live REST connection, moderator-only review actions, and abuse controls.

## Definition of MVP complete

A visitor can browse published pandals. A contributor can submit a new place or correction. Submissions stay private until approved. The same approved places appear on web and Android. Existing local photo GPS extraction continues to work.
