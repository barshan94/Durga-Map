# Durga Map

**Durga Map** is an Android-focused Durga Puja pandal and location explorer. It helps users inspect GPS coordinates available in selected photos, organize location records, view places on an interactive map, and open mapped locations for navigation. The project is also evolving toward a community-contributed map of public Durga Puja pandals.

> **Project status:** In progress. This repository contains the app source and Android build workflow. The portfolio demo is a browser-based walkthrough, not the installed Android application.

## Project links

- **Source code:** [GitHub repository](https://github.com/barshan94/Durga-Map)
- **Project guide:** [PROJECT_GUIDE.md](PROJECT_GUIDE.md)
- **Community roadmap:** [COMMUNITY_ROADMAP.md](COMMUNITY_ROADMAP.md)
- **Portfolio project page:** [Durga Map on the portfolio](https://portfolio-beta-eight-b4vx6dfs83.vercel.app/#projects)
- **Browser demo:** [Open the Durga Map walkthrough](https://portfolio-beta-eight-b4vx6dfs83.vercel.app/durga-map.html)
- **Android build workflow:** [GitHub Actions](https://github.com/barshan94/Durga-Map/actions)

The browser demo is illustrative and does not prove that every feature is connected to live backend data. For Android photo selection and device-specific behavior, install and test a built APK.

## What it does

- **Photo GPS inspection:** Select supported photo files and inspect available latitude/longitude metadata. A photo can only provide coordinates if usable GPS data is actually present.
- **Android photo and folder selection:** The Android build workflow adds a native file-picker bridge for selecting original photos or processing files from a selected folder.
- **Interactive map:** Browse recognized and saved locations on a Leaflet map.
- **Location inventory:** Review location records, coordinates, and status in a list/table.
- **Local records:** Keep device-local location records and export inventory data as CSV or JSON.
- **Shared location integration:** The frontend can request and submit location records through the configured Supabase backend, subject to its schema and Row Level Security (RLS) policies.
- **Account and personal journey UI:** The app includes account/session and personal visit-related interfaces; actual behavior depends on the current frontend and backend configuration.
- **Google Maps handoff:** Use mapped coordinates to open a location in an external map/navigation app where supported.
- **Android packaging:** Package the HTML application as an Android app with Capacitor.
- **Community-map direction:** Build toward a shared map with duplicate prevention, place verification, and moderated contributions.

## Technology

| Area | Technology |
|---|---|
| Frontend | HTML, CSS, JavaScript |
| Map interface | Leaflet |
| Android wrapper | Capacitor 6 |
| Android application ID | `com.barshan.durgamap` |
| App name | `Durga Map` |
| Web entry directory | `www` |
| Shared backend | Supabase (PostgreSQL, Auth, and Row Level Security) |
| Automated Android build | GitHub Actions, Node.js 20, Java 17 |

## Repository structure

```text
Durga-Map/
├── .github/
│   └── workflows/
│       └── build.yml             # Android APK build workflow
├── assets/
│   ├── durga-map-icon.svg        # App icon artwork
│   ├── durga-map-foreground.svg  # Foreground icon artwork
│   └── thumb.png                # Image asset used by the project
├── index.html                    # Root HTML file (not the workflow's current copy source)
├── durga-puja-map.html            # Main app source copied into the Android build
├── capacitor.config.json         # Capacitor app ID, name, and webDir
├── package.json                  # Capacitor dependencies
├── PROJECT_GUIDE.md              # Detailed usage, build, troubleshooting, and privacy guide
└── COMMUNITY_ROADMAP.md           # Shared-map roadmap and safety principles
```

The current workflow copies `durga-puja-map.html` to `www/index.html` when building the Android app. If you edit the app, make sure you update the source file used by the workflow; changing `index.html` alone may not change the packaged APK.

## Run the Android build

### Requirements

- Git
- Node.js 20 (the CI workflow uses Node.js 20)
- Java 17 (the CI workflow uses Temurin Java 17)
- Android SDK/build tools as required by Capacitor/Gradle for local builds
- Android device or emulator for installation and testing

### Option A — Build with GitHub Actions

This is the repository's configured build path and is recommended if you do not have the Android toolchain installed locally.

1. Open [GitHub Actions](https://github.com/barshan94/Durga-Map/actions).
2. Select the **Build APK** workflow.
3. Choose **Run workflow** when manual execution is available, or push a commit to `main` to trigger the workflow.
4. Wait for the workflow to complete successfully.
5. Open the successful run and download its APK artifact.
6. Install the APK on an Android device and test the changes on the device.

A successful source-code commit does not update an APK already installed on a phone. A new build must be generated and installed to test code changes.

### Option B — Prepare a local Capacitor build

Clone the repository:

```bash
git clone https://github.com/barshan94/Durga-Map.git
cd Durga-Map
npm install
```

The CI workflow is the source of truth for the current Android packaging steps. It prepares the `www` directory, copies the maintained app HTML and icon asset, adds the Android platform, and applies native photo-picker integration before the Gradle build. The repository's `package.json` does not define a standalone `build` script, so do not assume `npm run build` will build the APK.

For local Android builds, follow the steps in [`.github/workflows/build.yml`](.github/workflows/build.yml) and ensure the required Android SDK and Gradle tooling are installed.

## Using the app

### Inspect photo GPS

1. Prefer original camera photos, not screenshots or images re-saved by social apps.
2. Select a photo or supported folder in the Android app.
3. Review the extracted coordinates and confirm they are plausible before saving or sharing.
4. If GPS is missing, the photo may not contain GPS coordinates, its metadata may have been stripped, or the metadata parser may not support that file.
5. A GPS-related metadata label by itself does **not** guarantee valid latitude and longitude.

**Important:** Never guess coordinates when a photo's GPS data is absent or invalid. Confirm the location manually before contributing it to a shared map.

### Save and share a location

1. Confirm that the latitude and longitude are valid.
2. Add a pandal/place name when known.
3. Use the relevant save/contribution action.
4. Wait for an explicit success message and verify that the record was actually saved.
5. Treat a failed cloud save as a local draft rather than assuming it was shared.

### Local data versus shared data

- **Local records** are stored in the current browser/app storage on that device.
- **Shared records** are returned by the configured Supabase backend and are subject to database policies.
- Clearing local storage does not delete cloud records.
- Deleting cloud records does not automatically clear local app storage.
- The portfolio browser demo and the Android app may not have identical permissions or access to device files.

## Supabase and configuration

The app includes frontend integration for shared locations and account-related functionality. Its live behavior depends on the Supabase project URL, the client-side publishable/anon key, table schema, and RLS policies configured for the project.

Before using or deploying a copy:

- Confirm the frontend points to the intended **Durga Map** Supabase project.
- Confirm the expected tables and columns match the current app code.
- Test read/write behavior as signed-out and signed-in users.
- Ensure unreviewed submissions are not exposed as published public places.
- Never place a Supabase service-role key or other server secret in client-side HTML.
- Keep authentication/session data and personal visits protected by least-privilege policies.

The community roadmap describes the intended submission and moderation model. Do not assume every planned moderation or anti-abuse feature is complete just because it appears in the roadmap.

## Community-map principles

The longer-term goal is to make approved public pandal locations discoverable across devices while keeping contributor privacy in mind.

- A contribution should represent a place/pandal, not merely duplicate photo pins.
- Prevent duplicate locations and validate coordinates.
- Keep pending submissions private until reviewed.
- Keep moderator-only actions off the public client.
- Do not upload original photos automatically; photo upload should require explicit user consent and suitable access controls.
- Do not display contributor account IDs or private profile details publicly by default.
- Keep local photo processing separate from public sharing.

See [COMMUNITY_ROADMAP.md](COMMUNITY_ROADMAP.md) for milestones and the proposed data model.

## Privacy and safety

- Photo metadata can reveal sensitive locations. Inspect coordinates before sharing them.
- Only share locations that are appropriate to publish; avoid exposing a private home or other sensitive place.
- Do not assume that an extracted location has been uploaded or approved.
- Do not use local storage as the only backup for important records.
- Review database access policies before enabling community contributions at scale.

## Troubleshooting

### GPS coordinates are missing or incorrect
- Try the original photo file.
- Check whether location tagging was enabled when the photo was taken.
- Compare with another known GPS-tagged photo.
- Do not treat missing or invalid values as real coordinates.

### Old places still appear after cloud data was cleared
- Check whether they are saved in device-local storage.
- Confirm the installed APK includes the latest frontend.
- Verify the shared-location request and its response separately from the local inventory.

### A location is not visible on another device
- Confirm the save action reported success.
- Verify that the row exists in Supabase.
- Check that the intended read policy permits the other device/session to retrieve it.
- Confirm that the place is published, not pending moderation.

### Changes do not appear in the installed Android app
- Check the latest GitHub Actions run.
- Confirm the workflow copied `durga-puja-map.html` into `www/index.html`.
- Download and install the newly generated APK.
- Test the exact changed feature on the device.

## Current limitations

- The project is in progress; not every roadmap item is necessarily implemented or fully tested.
- GPS extraction depends on the selected file containing readable coordinates.
- Shared-map behavior depends on live Supabase configuration and access policies.
- Community submissions need a properly tested moderator-only review workflow before the map can be treated as a fully moderated public directory.
- Browser-demo behavior is not a substitute for Android device testing.
- Local and cloud records are separate and can become inconsistent if not checked explicitly.

## Author

**Barshan Ghosh**

- [GitHub profile](https://github.com/barshan94)
- [Portfolio](https://portfolio-beta-eight-b4vx6dfs83.vercel.app/)

---

For a deeper operational reference, read [PROJECT_GUIDE.md](PROJECT_GUIDE.md). For planned community contribution and moderation work, read [COMMUNITY_ROADMAP.md](COMMUNITY_ROADMAP.md).
