# Durga Map — Project Guide

Last updated: 25 October 2026

## 1. What this project is

Durga Map is an Android-only Durga Puja pandal/location explorer. The standalone web version was removed because browser-based photo-location access did not work reliably for this use case. Its main purpose is to help people identify and find Puja mandaps using GPS coordinates extracted from selected photos, and to build toward a shared map of public pandal locations.

Repository: https://github.com/barshan94/Durga-Map

Android package ID: `com.barshan.durgamap`  
App name: `Durga Map`  
Main technology: static HTML/CSS/JavaScript, Leaflet map UI, Capacitor Android wrapper, and Supabase for shared data and account features.

## 2. Features in the current project

- **Photo GPS workflow:** select photos and inspect available metadata to find latitude/longitude. GPS extraction only works when the selected image actually contains readable GPS coordinates; a GPS-related metadata label by itself does not guarantee coordinates are present or readable.
- **Map and location inventory:** view recognized locations on a map and in a list/table, including coordinates and status.
- **Local device records:** draft/saved location records can be stored in the Android app's Capacitor WebView storage. These records are specific to that app installation/device.
- **Shared public locations:** the app requests public location rows from the configured Supabase project and displays locations returned by the backend.
- **Account/session support:** account UI and session handling are present in the HTML application.
- **Location contribution flow:** code includes saving location records to the shared backend, checking for duplicates, and verifying that a write was actually returned by the backend.
- **CSV/JSON export and local clearing controls:** the inventory UI includes export actions and a “Clear local locations” control. Read the caution below before using the clear control.
- **Android packaging:** Capacitor is configured with app ID `com.barshan.durgamap`; the GitHub Actions workflow is under `.github/workflows/build.yml`.
- **Custom launcher icon assets:** `assets/thumb.png`, `assets/durga-map-icon.svg`, and `assets/durga-map-foreground.svg` are present in the repository.

## 3. Repository map

- `durga-puja-map.html` — maintained application HTML source with the map, photo workflow, inventory, local storage, and backend integration.
- `index.html` — legacy/root HTML file; confirm the workflow's copy step before editing it as the app source.
- `capacitor.config.json` — Capacitor app ID/name and configured web directory (`www`).
- `package.json` — Capacitor dependencies and project package metadata.
- `.github/workflows/build.yml` — Android build workflow.
- `assets/thumb.png` — supplied image asset used in the app/icon workflow.
- `assets/durga-map-icon.svg`, `assets/durga-map-foreground.svg` — vector icon artwork.
- `COMMUNITY_ROADMAP.md` — longer-term roadmap for community contributions, moderation, and privacy.

The current workflow copies `durga-puja-map.html` into `www/index.html` before packaging. Make app behavior changes in the maintained source file and verify the workflow still packages it.

## 4. How to use the app

### Explore locations
1. Open Durga Map and go to the map/public map or location inventory view.
2. Review the map markers, location names, coordinates, and status.
3. Use the location filter/search field if you need to find a particular name.
4. Use CSV or JSON export if you need a copy of the displayed inventory.

### Extract GPS from photos
1. Select the original photo file on your device. Prefer the original image from the camera/gallery rather than a screenshot or a copy sent through an app that may remove metadata.
2. Check the GPS result and coordinates shown by the app.
3. If the app says GPS is missing, the image may not contain GPS coordinates, the coordinates may have been stripped, or the parser may not support the metadata layout. A generic “GPS” metadata field alone is not proof of usable latitude/longitude.
4. Do not assume a location was saved to the shared map just because it was detected locally.

### Save/share a location
1. Confirm the location has valid latitude and longitude.
2. Enter the pandal/place name if known.
3. Use the app's save/publish action and wait for its explicit success message.
4. If the app reports a cloud-save failure, treat the item as a local draft until the backend write is verified.

### Local versus shared data
- **Local/device data** lives in the Android app's WebView storage on one device.
- **Shared data** comes from the configured Supabase project and should be available across devices when access policies permit.
- Clearing the cloud database does not automatically erase local storage on phones.
- Clearing the app's local storage does not delete rows in Supabase.
- Reinstalling an app can sometimes leave or clear local data depending on the Android/WebView storage behavior; do not use reinstalling as the only data-cleanup strategy.

## 5. Important current data-cleanup note

The configured Supabase project is the Durga Map project with ID `iomikfkjawupfyppzamj` (region `ap-south-1`). The app source uses that project's Supabase URL.

A cleanup was performed on 25 October 2026. The following public application tables were verified empty afterward:

- `public.puja_locations`
- `public.personal_visits`
- `public.personal_pandal_status`
- `public.pandal_submissions`
- `public.pandals`

The existing Supabase authentication account was preserved. Tables, schema, and authentication were not intentionally deleted.

If old mandap names still appear after cloud cleanup, first check whether they are stale local cached records or whether the installed APK is running older HTML. Do not delete the auth account or recreate the database as a first response.

The application stores location cache data under the browser local-storage key `durgaMap.locations.v2`. The old `durgaMap.records.v1` key is removed by the application when loading saved records. Local cache and cloud rows are separate data sources.

## 6. Build and update workflow

1. Open the repository's **Actions** tab: https://github.com/barshan94/Durga-Map/actions
2. Check the latest run of `.github/workflows/build.yml`.
3. Wait for the run to finish and confirm it succeeded.
4. Download the APK artifact produced by the successful run.
5. Install/update that APK on Android and test the exact behavior changed.
6. If an app behavior has not changed, verify that the workflow packaged the edited source file and that the installed app was updated. A GitHub source change does not modify an APK already installed on a phone.

For local development, the repository currently lists Capacitor Android/CLI/Core and Geolocation dependencies in `package.json`. Use the build workflow as the source of truth for exact build commands and required tooling.

## 7. Troubleshooting

### Old mandap names still appear after the database was cleared
- Confirm the app is updated to a build containing the latest code.
- Check local storage for `durgaMap.locations.v2`.
- Confirm the shared-location request succeeds and returns an empty list.
- Check whether the inventory is displaying locally saved records separately from cloud locations.
- Avoid repeatedly clearing the database; the remaining data may be local to the phone.

### A photo loads but GPS is missing
- Try the original photo, not a screenshot or social-media copy.
- Confirm location tagging was enabled on the camera when the photo was taken.
- Test a second photo known to contain coordinates.
- If multiple known GPS-tagged originals fail, treat it as a parser issue and inspect the EXIF extraction path.

### A saved location does not appear on another device
- Confirm the app displayed a successful cloud-save/verification message.
- Confirm the location row exists in Supabase.
- Confirm the public read policy allows the intended query.
- Distinguish a local draft from a shared/published location.

### The APK still shows an old launcher icon
- Confirm the current build includes the icon assets and Android resource-generation steps.
- Install the newly built APK, not an older downloaded artifact.
- If Android keeps the old icon, verify the installed package/version and test after updating or reinstalling only when local data is safely backed up.

## 8. Privacy and safety rules

- Do not upload original photos automatically. Photo metadata can reveal sensitive locations.
- Ask for explicit consent before any future photo-upload feature.
- Do not expose contributor account IDs or private profile information publicly.
- Keep moderation/admin actions off the public client.
- Validate coordinates and avoid duplicate place records.
- Never put a Supabase service-role key or other server secret in client-side HTML. A publishable/anon key is intended for client use only when paired with correct Row Level Security policies.
- Before a destructive database change, inspect the affected rows, explain what will be deleted, and preserve authentication unless the owner explicitly requests otherwise.

## 9. Current limitations and next priorities

- The installed APK must be rebuilt to receive source changes.
- Confirm that `index.html` and `durga-puja-map.html` stay synchronized with the actual build input.
- Re-test the stale cached public-location behavior on Android after installing a build that includes the fix.
- Re-test GPS extraction using original photos known to contain GPS coordinates.
- Verify Supabase Row Level Security and sign-in/sign-out behavior with both authenticated and signed-out sessions.
- Complete the moderator-only review flow before treating community submissions as a fully moderated public service.
- Test the packaged Android APK on a physical device; standalone web behavior is not supported.

## 10. Definition of a reliable release

A release is ready only after:
- the build workflow succeeds;
- the new APK is installed and identified as the latest build;
- photo GPS extraction is tested with a known GPS-tagged original;
- local and shared location lists behave correctly after cloud deletion;
- location saving is verified against Supabase;
- account/session behavior is tested;
- no private/pending contribution data is exposed publicly.
