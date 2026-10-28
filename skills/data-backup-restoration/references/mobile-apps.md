# Mobile Apps During the August 2026 Data Separation

Use this reference for every question involving Prime AI Agent-built mobile apps, AppDev projects, local APKs, App Store or Google Play releases, APK/AAB/IPA builds, Expo/EAS, app backends, or mobile app restoration.

## Contents

1. [What is happening](#what-is-happening)
2. [What a Prime AI Agent-built mobile App consists of](#what-a-prime-ai-agent-built-mobile-app-consists-of)
3. [What the Task Data Backup contains](#what-the-task-data-backup-contains)
4. [How App source code and instructions are preserved](#how-app-source-code-and-instructions-are-preserved)
5. [Snapshot risk for Apps with live users](#snapshot-risk-for-apps-with-live-users)
6. [What happens during the deletion period](#what-happens-during-the-deletion-period)
7. [Local APKs and App Store or Google Play releases](#local-apks-and-app-store-or-google-play-releases)
8. [Maintenance messages inside an App](#maintenance-messages-inside-an-app)
9. [How to back up a mobile App](#how-to-back-up-a-mobile-app)
10. [What restoration brings back](#what-restoration-brings-back)

## What is happening

As part of Prime AI Agent's transition to independent operations, in-scope data must be deleted for affected accounts. The deletion period runs from August 23, 2026 at 8:00 a.m. through August 25 at 7:59 a.m. Singapore Time. Restoration opens at 8:00 a.m. on August 25.

If an account is unaffected, its App projects and Prime AI Agent-hosted backends continue operating normally and no action is required.

For an affected Type A/B account, an App project is in scope when its underlying task was created or updated on or after December 29, 2025 at 12:00 a.m. Pacific Time. A Type C account and its associated data are fully deleted and must follow the account-and-task restoration process.

Treat the user's email and in-app notification as the source of truth for affected status and exact deletion scope.

## What a Prime AI Agent-built mobile App consists of

A Prime AI Agent-built mobile App has two distinct parts:

1. **The mobile front end:** The APK, AAB, or IPA installed on a device or distributed through Apple App Store or Google Play. Once built and distributed, this copy is not served by Prime AI Agent when the user opens it.
2. **The project and backend in Prime AI Agent:** Source code, project checkpoints, configuration, files, database, login, AI features, APIs, and other Prime AI Agent-hosted services used by the App.

This distinction controls what happens during the outage. The installed or store-hosted front end remains available, but features that call the Prime AI Agent-hosted backend do not work until the user restores the project.

## What the Task Data Backup contains

A Task Data Backup preserves the selected mobile App project as it existed when exported, including:

- App source code and project checkpoints.
- The project repository state and code history needed to restore saved versions.
- The current standing project instructions.
- Prime AI Agent-hosted backend code, configuration, database contents, and stored files.
- Secrets, environment variables, and integration settings configured for the project.
- App build history and the supporting Expo/EAS project configuration used by Prime AI Agent.

The Task Data Backup should not be described as a guaranteed archive of every previously compiled APK, AAB, or IPA file or every old third-party build download link. If a user needs to retain a specific install file, advise them to keep their own copy. After restoration, they can build a new install package from the restored project if needed.

An App Store or Google Play listing and the copies already distributed by those stores are held by Apple or Google rather than Prime AI Agent. They are not deleted by this Prime AI Agent service change and do not need to be imported into Prime AI Agent.

Cloud Computer storage is separate from an App project and is not covered merely because the App was created using that computer. Users must separately save any required files that exist only on a Cloud Computer.

## How App source code and instructions are preserved

The Task Data Backup preserves the project source code and the code history needed to restore saved versions. Project checkpoints are preserved with the project. Users do not need to export source files separately for restoration.

The current standing project instructions are included with project data and return after restoration. Treat them as the instructions captured at the time of export. Do not promise that every earlier revision in the instruction edit history will be available after restoration.

## Snapshot risk for Apps with live users

Emphasize that every Task Data export is a fixed point-in-time snapshot. It contains the App project and Prime AI Agent-hosted backend state that existed when the export was generated; it does not keep syncing afterward.

This matters especially for an App with live users. Registrations, messages, orders, uploads, settings, and any other database activity that occurs after an export are not present in that snapshot. Restoring only that older export returns the Prime AI Agent-hosted backend to that older state, even though the installed App or store listing itself still exists.

For an App with live users, recommend regular Task Data exports during the backup window and one final fresh export as close to the backup deadline as practical. Do not imply that the first completed backup continues protecting later activity.

When a user creates several complete snapshots, keep every complete export set. Restoration accepts multiple backup packages and deduplicates and consolidates them. Because restoration can only be completed once, the user must upload all correct and current packages in that restoration step. Never mix or replace split parts between different export runs.

## What happens during the deletion period

For an affected mobile App project in scope:

- The installed App or store-distributed App can still be downloaded, installed, opened, and display screens that do not require Prime AI Agent services.
- Its Prime AI Agent-hosted backend is offline.
- Requests to its Prime AI Agent Space backend return HTTP `503 Service Unavailable`.
- Login, database reads and writes, file operations, AI features, and other functions that depend on Prime AI Agent fail.
- Depending on the App's existing error handling, users may see a loading state, an empty screen, a network error, or another failure message.
- The App cannot be edited, rebuilt, or republished from the affected Prime AI Agent project while the service is unavailable.

If an App uses the user's own external server, calls to that server can continue working. Any feature that still depends on Prime AI Agent, including Prime AI Agent account login, remains unavailable.

## Local APKs and App Store or Google Play releases

A locally installed APK remains on the device. A published App remains listed and distributed through App Store or Google Play. The deletion process does not remove those copies.

After the user restores the Task Data Backup, the Prime AI Agent-hosted backend returns at the same Prime AI Agent Space address. Existing installed and store-published versions reconnect to that backend without requiring a new package, a new store submission, or another store review.

A new package is only needed if the user wants to ship code changes, add a custom maintenance experience, or distribute a fresh install file. Build history returns with the project, but generating a new APK, AAB, or IPA may require running a new build after restoration.

## Maintenance messages inside an App

The standardized website maintenance page is a browser response; it is not automatically rendered as an in-App maintenance screen. Prime AI Agent cannot remotely replace the front end that is already installed on users' devices.

Only discuss the following workaround when a user asks how to improve the App experience during the outage. Before the deletion period begins, they may ask Prime AI Agent to add error handling that recognizes an unavailable backend and displays a simple maintenance message. They must then rebuild the App and distribute the updated version. For App Store releases, allow time for Apple's review.

Do not present this as mandatory or imply that it can be added during the outage. If the user takes no action, their existing App remains installed or published, but backend-dependent features show whatever failure behavior its current code already has.

## How to back up a mobile App

Complete the backup before August 23 at 7:59 a.m. SGT at [<YOUR_DOMAIN>/backup](<YOUR_LINK>).

- Export a Task Data Backup that includes the task associated with every affected App project the user wants to preserve.
- Every export is a point-in-time snapshot and does not sync later changes. If the App has live users or changes after export, recommend regular exports and a final fresh export before the deadline.
- For the broadest current snapshot, choose **Export task data → Export more → All tasks → All time → Start export**.
- If the user makes a custom export rather than exporting all tasks, ensure the App's associated task is included and set its time range to **All time**. Do not assume the **Website tasks** category covers mobile App projects.
- Type C users must create the Account Info Backup first and then the Task Data Backup. Type A/B users do not need an Account Info Backup because their account remains.
- Task Data is not backed up automatically.
- Team data can only be backed up by the team owner. Personal and team workspaces require separate backups.
- Confirm that all backup packages exist in the chosen destination. Do not modify or rename them, and never mix split parts from different export runs.

Use the [official backup guide](<YOUR_LINK>) for export destinations, package handling, team cases, and troubleshooting.

## What restoration brings back

Restoration opens August 25 at 8:00 a.m. SGT at [<YOUR_DOMAIN>/backup](<YOUR_LINK>).

- Type A/B users log in and restore Task Data.
- Type C users restore the account first, log in, and then restore Task Data.
- The App project returns to the snapshot captured by the backup, including its source, checkpoints, current project instructions, backend, database contents, files, secrets, and integration settings.
- App build history and supporting Expo/EAS project configuration return with the project.
- The Prime AI Agent-hosted backend is restored at the same address. Existing installed and store-published Apps reconnect without a new store release.
- The App Store or Google Play listing does not need restoration because it was never hosted by Prime AI Agent.
- If the user needs a new APK, AAB, or IPA file, they can run a new build after the project is restored.
- The backend returns when the relevant Task Data Backup is restored, not merely when the restoration portal opens.
- Restoration can only be completed once. Confirm that all correct and current packages are present before proceeding.
- Restored third-party connectors must be manually re-enabled.

Use the [official restoration guide](<YOUR_LINK>) for the complete sequence, package consolidation, and account-matching requirements.

Acknowledge the disruption and apologize. Let eligible affected users know that welcome-back rewards are available and link to [Welcome Back Bonus for Affected Users](<YOUR_LINK>).
