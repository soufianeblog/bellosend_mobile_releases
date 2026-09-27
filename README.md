# BelloSend for Android — releases

The published builds of the BelloSend Android app, and the one file every
installed copy reads to find out whether it is out of date.

BelloSend is not distributed through Google Play. Each app checks
[`latest.json`](latest.json) here — quietly on start-up, and whenever someone
opens **Settings → About** — and offers the build it names.

## `latest.json`

```json
{
  "version": "0.0.18",
  "build": 18,
  "url": "https://github.com/soufianeblog/bellosend_mobile_releases/releases/download/v0.0.18/bellosend-0.0.18-universal.apk",
  "sha256": "…64 hex characters…",
  "size": 117440512,
  "min_build": 0,
  "published_at": "2026-09-27T10:00:00Z",
  "notes": ["What changed", "And this"]
}
```

| field | meaning |
| --- | --- |
| `build` | decides "newer": a phone on a lower build is offered this one. Must only ever increase. |
| `version` | what a person reads in the app. |
| `url` | the APK. Must be `https` on a GitHub host — the app rejects anything else. |
| `sha256` | of that exact file. A download that does not match is deleted, never installed. |
| `size` | bytes, shown before the download starts and used for the progress bar. |
| `min_build` | below this the update stops being optional. `0` means everything still works. |
| `notes` | bullet points shown in the app. |

## Publishing a build

Never by hand. From the app repository:

```bash
cd bellosend_mobile
# bump both halves of version: in pubspec.yaml, commit, then:
scripts/release.sh "What changed"
```

That builds the signed, obfuscated APK, attaches it to a GitHub release here,
and updates `latest.json` **last** — so a failed upload never leaves phones
chasing a release that does not exist. The full procedure, including signing
and debug symbols, is in the app repository at `docs/RELEASING.md`.

## Rolling back

Publish the previous build again under a **new, higher** build number. Never
lower `build` and never delete a release people may be running: Android will
not install a lower `versionCode` over a higher one, so those phones would be
offered an update they cannot apply.

## What is safe to put here

Only the APKs and this manifest. The repository is public, which is what lets
every installed app read it without carrying a token to do so.

Never publish the debug symbols (`symbols/`) from a build: they undo the
obfuscation the release was built with. They live in the private app
repository, beside the tag for that release.
