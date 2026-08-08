# Tasks

A task list and a calendar that share one set of tasks. No accounts, no server, no internet
needed — everything lives on your phone.

## What's in the box

| File | What it does |
|---|---|
| `index.html` | The whole app. HTML, CSS and JavaScript in one file, no build step. |
| `manifest.webmanifest` | Name, icon and colours used when you install it to your home screen. |
| `sw.js` | Caches the app so it opens instantly and works offline. |
| `icon-*.png` | App icons. |
| `package.json`, `capacitor.config.json` | Only needed for the APK route below. |

To try it on your computer first: open `index.html` in any browser, or run `npm run serve`
from this folder and open the address it prints.

---

## The three kinds of task

**Task** — happens on one day. Pick the day, optionally a time. Shows up on that day, and
nowhere else.

**Deadline** — has a due date *and* a day the reminders start. It then appears on every day
in between, counting down ("3 days left"), and the bar in the calendar grid brightens as
the date approaches. Ticking it off once clears the whole run.

**Repeating** — pick the first occurrence and an interval of 1 to 7 days. Set 7 starting on
a Sunday and it appears every Sunday from then on, indefinitely. Each occurrence is ticked
off separately, so finishing last Sunday's doesn't touch next Sunday's.

Everything you tick is kept in **Done**, grouped by the day you finished it, and stays
visible and ticked on the calendar.

---

## Route 1 — Install it as an app (5 minutes, no tools)

This gives you a real home-screen icon, offline use, and no browser chrome. It does **not**
give you notifications; for those, use route 2.

1. Put these files somewhere with an HTTPS address. The easiest free option is GitHub Pages:
   create a repository, upload every file in this folder to the root, then in
   **Settings → Pages** set the source to your `main` branch and `/root`.
2. Open the address GitHub gives you in **Chrome on your phone**.
3. Menu (⋮) → **Add to Home screen** → **Install**.

It has to be HTTPS — the offline caching won't switch on over plain `http://`.
Opening the file directly from your phone's storage also won't work; it needs to be served.

## Route 2 — Build an APK (adds notifications)

Needs [Node.js](https://nodejs.org) and [Android Studio](https://developer.android.com/studio)
on your computer. From this folder:

```bash
npm install
npx cap add android
npx cap sync android
npx cap open android
```

The last command opens the project in Android Studio. Press **Run** with your phone plugged
in and USB debugging on, or use **Build → Build Bundle(s) / APK(s) → Build APK(s)** to get
an installable file.

Before you build, open `capacitor.config.json` and change `appId` from
`com.yourname.tasks` to something of your own — it has to be unique and can't be changed
after you publish anywhere.

The app already schedules notifications for any task with a time set (the next 30 days,
rescheduled whenever you change something). Android will ask permission the first time.
On Android 13 and up, also allow notifications in Settings if you dismissed that prompt.

---

## Changing things

Everything is in `index.html`, in labelled sections.

- **Colours** — the `:root` block at the top. `--single`, `--deadline` and `--periodic` are
  the three category colours; change them in one place and they update everywhere.
- **How far the list looks ahead** — `HORIZON` (60 days) and `PAST` (120 days) near the top
  of the script.
- **Which day the week starts on** — the calendar starts on Sunday. To start on Monday,
  change `DOW` to begin with `'Mon'` and adjust `offset` in `monthView` to
  `(new Date(y,m,1).getDay() + 6) % 7`.
- **The recurrence rules themselves** — `occurrencesOn()`. Every view is built from it, so a
  change there shows up in the calendar, the list and the notifications at once.

If you edit any file after installing, bump `CACHE` in `sw.js` (e.g. `tasks-v2`), or the old
cached copy will keep loading.

## Your data

Tasks are stored in the browser's local storage on the device. They are not synced or
backed up anywhere. Clearing the browser's site data, or uninstalling the app, deletes them.
The reset button in the top right wipes everything — it asks once, then does it.
