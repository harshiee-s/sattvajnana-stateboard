# Sri Hari Sattva Jnana — AI-Integrated Learning Portal

Class 10 State board learning portal for admins, schools and students:
AI Knowledge Base, Learning Lessons, Audio Explanations, Mock-Question Papers,
Question Bank, Previous Year Question Papers, progress tracking, notices,
usage reports and school / student management.

It is a plain static website (no build step). Firebase (Authentication,
Firestore, App Check) is used straight from the browser.

## Folder structure

```
├── index.html                 # Page markup; loads the CSS and JS below
├── favicon.ico
├── firestore.rules            # Security rules — publish in Firebase console
├── README.md
│
├── css/                       # Loaded in this order (order matters)
│   ├── variables.css          # Theme colours, glass materials, radii, shadows
│   ├── themes.css             # Dark theme token overrides
│   ├── base.css               # Reset, background, fonts, scrollbars
│   ├── auth.css               # Loading screen, sign-in screen, form fields
│   ├── layout.css             # App shell: sidebar, top bar, search, pages
│   ├── dashboard.css          # Dashboard statistic cards
│   ├── components.css         # Panels, tables, pills, buttons, toolbars
│   ├── resources.css          # Video, audio player, folders/files, full screen
│   ├── pages.css              # Progress pages, Notice Board, empty states
│   ├── modals.css             # Modal dialogs, pop-ups, toasts
│   └── mobile.css             # Phones: compact layout, bottom tab bar
│
├── js/                        # Loaded in this order (order matters)
│   ├── theme.js               # In <head>: saved theme + toggle button
│   ├── firebase.js            # First (module): Firebase SDK, App Check, app/auth/db
│   ├── utils.js               # Toast, escaping, dates, empty states, no-zoom
│   ├── permissions.js         # Admin / school / student access, Section Access
│   ├── forms.js               # Form fields, cascading dropdowns, validation
│   ├── modals.js              # Closing forms safely
│   ├── auth.js                # Sign-in, sign-out, one-device sessions, logins
│   ├── profile.js             # Profile, avatar, greeting, admin settings
│   ├── navigation.js          # Sidebar, bottom tabs, back button, exit question
│   ├── search.js              # Global search
│   ├── entities.js            # Schools, students, subjects, chapters, topics,
│   │                          #   Create User, bulk import, school logins
│   ├── notifications.js       # Notifications and Notice Board
│   ├── resources.js           # Materials, lessons, audio, uploads, viewers
│   ├── question-bank.js       # Question bank, mock papers, past papers
│   ├── progress.js            # Progress tracking and usage tracking
│   ├── dashboard.js           # Dashboard statistics and Usage Reports
│   └── main.js                # Last: starts the app (sign-in listener)
│
├── images/
│   ├── logo.png
│   ├── favicon.png
│   └── … (banner and dashboard images already on the live site)
│
└── assets/
    ├── home-1.png             # Sign-in screen picture  (copy from the old site)
    └── bck-lg.png             # Sign-in background      (copy from the old site)
```

## How the JavaScript fits together

- `firebase.js` is an ES module. It imports the Firebase SDK from Google's CDN,
  starts App Check, creates `app`, `auth` and `db`, and shares them — plus all
  SDK functions — through `globalThis`.
- Every other file is a normal script with `defer`. Browsers run deferred
  scripts and modules **in the order they appear**, after the page is parsed,
  so each file can use everything from the files before it.
- All files share one global scope: a function or variable declared in one
  file can be used in any other file **when it runs** (on a click, a Firebase
  update, etc.).
- `main.js` runs last and registers the sign-in listener. Anything that starts
  the app or waits on Firebase belongs in `main.js`, so it can never run before
  the code it needs has loaded.

### Adding code

- Put new code in the file that matches its job.
- Don't declare the same top-level name in two files — the page will stop
  with an error.
- At the top level of a file, only *define* things or attach event listeners.
  Code that runs straight away and needs another file should go in `main.js`.

## Deploying

1. Upload **everything** in this folder, keeping the folders as they are.
2. Copy `home-1.png` and `bck-lg.png` from the old site into `assets/`.
3. After changing any CSS or JS file, change the `?v=20261024` number on its
   `<link>` / `<script>` line in `index.html` (for example to today's date),
   so browsers fetch the new file instead of an old cached copy.

## Firebase settings that matter

- **Firebase config / App Check site key:** top of `js/firebase.js`.
  Both are public by design; access is controlled by `firestore.rules`.
- **App Check** is enforced for Cloud Firestore and Authentication. Pages that
  use Firebase from the browser must start App Check (this portal does).
- **Security rules:** publish `firestore.rules` in Firebase console →
  Firestore → Rules whenever it changes. Upload the site first, then rules.

## Testing on your own computer

- Open the folder with a local web server (e.g. VS Code → *Open with Live
  Server*). Opening `index.html` straight from the file system does **not**
  work: browsers block module scripts loaded from `file://`.
- Locally, App Check uses a debug token printed in the browser console. Add it
  in Firebase console → App Check → Apps → *Manage debug tokens*. Keep it
  secret and never put it in the code.
