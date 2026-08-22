# Naukri Resume Updater

A local Playwright automation that re-uploads the same resume to your Naukri profile.

The project is designed to run on your own computer using your normal network connection.

## What it does

The automation:

1. Opens your Naukri profile.
2. Uses a saved Playwright authentication session.
3. Uploads the resume from `resume/resume.pdf`.
4. Clicks a Save/Update button when required.
5. Saves a screenshot and result information under `artifacts/`.

The browser is run in headed mode by default for local use because Naukri may present an access block when the browser is run headlessly.

## Requirements

- Windows, macOS, or Linux
- Node.js 22 or newer
- npm
- A Naukri account
- The resume you want to upload as a PDF

## Project structure

```text
naukri-profile-updater/
├── scripts/
│   ├── setup-session.ts
│   └── update-resume.ts
├── playwright/
│   └── .auth/
│       └── state.json
├── resume/
│   └── resume.pdf
├── artifacts/
├── package.json
├── package-lock.json
├── tsconfig.json
└── README.md
```

## Installation

Clone or download the project and open a terminal in the project directory.

Install the Node.js dependencies:

```bash
npm install
```

Install the Chromium browser used by Playwright:

```bash
npx playwright install chromium
```

## Add your resume

Place the resume you want Naukri to upload at:

```text
resume/resume.pdf
```

The current automation validates that the resume is no larger than 300 KB.

## Create the Naukri authentication session

The automation needs an authenticated browser session. You should create this locally instead of putting your Naukri credentials into the project.

Run:

```bash
npm run bootstrap
```

A browser window will open.

Log into Naukri normally. Complete any OTP or CAPTCHA manually if Naukri asks for it.

After logging in, follow the instructions shown by the bootstrap script. The authenticated Playwright state will be saved to:

```text
playwright/.auth/state.json
```

### Security warning

`state.json` contains authentication/session information. Treat it like a password.

**Never commit it to Git.**

Your `.gitignore` should contain:

```gitignore
node_modules/
playwright/.auth/
artifacts/
*.log
.DS_Store
.env
```

## Run the updater

Run:

```bash
npm run update
```

For local execution, the browser should run in headed mode so you can see what Playwright is doing.

The update process will:

```text
Start Chromium
    ↓
Load saved Naukri session
    ↓
Open Naukri profile
    ↓
Find resume upload input
    ↓
Upload resume.pdf
    ↓
Save/Update if required
    ↓
Wait for the update
    ↓
Save diagnostic artifacts
```

## Environment variables

The updater supports these environment variables:

| Variable | Default | Purpose |
|---|---|---|
| `HEADLESS` | `false` | Set to `true` to run the browser without any UI |
| `RESUME_PATH` | `resume/resume.pdf` | Path to the resume |
| `AUTH_STATE_PATH` | `playwright/.auth/state.json` | Path to the Playwright authentication state |
| `ARTIFACT_DIR` | `artifacts` | Directory for screenshots and result files |

For local use, headed mode is recommended:

### PowerShell

```powershell
$env:HEADLESS="false"
$env:RESUME_PATH="resume/resume.pdf"
$env:AUTH_STATE_PATH="playwright/.auth/state.json"
$env:ARTIFACT_DIR="artifacts"

npm run update
```

### Command Prompt

```cmd
set HEADLESS=false
set RESUME_PATH=resume/resume.pdf
set AUTH_STATE_PATH=playwright/.auth/state.json
set ARTIFACT_DIR=artifacts

npm run update
```

### macOS / Linux

```bash
HEADLESS=false \
RESUME_PATH=resume/resume.pdf \
AUTH_STATE_PATH=playwright/.auth/state.json \
ARTIFACT_DIR=artifacts \
npm run update
```

## Artifacts

The updater creates an `artifacts/` directory.

A successful run can produce:

```text
artifacts/
├── success.png
└── result.txt
```

If navigation or the automation fails, diagnostic files can include:

```text
artifacts/
├── failure-*.png
├── failure-*.html
├── failure-*.txt
└── error.txt
```

These files are useful for troubleshooting Naukri page changes or unexpected errors.

## Re-authentication

The saved authentication session may eventually expire.

If the updater reports that the authenticated Naukri session is unavailable or expired, run:

```bash
npm run bootstrap
```

Log into Naukri again and create a fresh `state.json`.

## CAPTCHA and access blocks

The automation does not attempt to bypass CAPTCHA or other access-control mechanisms.

If Naukri presents a CAPTCHA or access-block page, stop the automation and complete the required verification manually. After that, recreate the saved authentication session if necessary.

## Useful commands

Install dependencies:

```bash
npm install
```

Install Chromium:

```bash
npx playwright install chromium
```

Create/update the saved Naukri session:

```bash
npm run bootstrap
```

Upload the resume:

```bash
npm run update
```

## Troubleshooting

### `Playwright authentication state not found`

Make sure this file exists:

```text
playwright/.auth/state.json
```

If it does not exist, run:

```bash
npm run bootstrap
```

### `Could not find the Naukri resume file input`

Naukri may have changed its profile page. The upload selectors in `scripts/update-resume.ts` may need to be updated.

### CAPTCHA or access-block message

The site is asking for additional verification or is blocking the automated browser. Do not attempt to bypass it. Run the browser visibly and complete any required verification manually.

### Resume upload succeeds but no timestamp appears immediately

Wait a few seconds and refresh the Naukri profile manually to verify that the resume update was recorded.

## Important files

### `scripts/bootstrap-session.ts`

Used to create the authenticated Playwright session. Normally run only during initial setup or when the session expires.

### `scripts/update-resume.ts`

Contains the actual Naukri resume upload automation. This is the script that should be run whenever you want to update the profile.

### `playwright/.auth/state.json`

Stores the authenticated browser state. This file is sensitive and must never be committed.

### `package.json`

Contains project dependencies and npm scripts.

### `package-lock.json`

Locks dependency versions so the project can be reproduced consistently.

### `tsconfig.json`

Contains the TypeScript compiler configuration.

## Manual daily use

To update the profile manually from your computer, run:

```bash
npm run update
```

For convenience on Windows, you can create a small `.ps1` script containing the environment variables and the `npm run update` command, then run that script whenever you want to perform the update.
