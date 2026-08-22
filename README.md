# Naukri Profile Auto Updater

Automatically re-uploads the same resume to the Naukri profile page every day using Playwright and GitHub Actions.

## What it does

1. Opens the Naukri profile page with an authenticated Playwright session.
2. Finds the resume upload input.
3. Uploads `resume/resume.pdf`.
4. Clicks a visible Save/Update button if one is present.
5. Captures a screenshot and a small result log.
6. Fails without bypassing CAPTCHA or access-block pages.

## 1. Put your resume in the repo

Add your PDF as:

`resume/resume.pdf`

Keep it at or below 300 KB.

## 2. Install locally

```bash
npm install
npx playwright install chromium
```

## 3. Create the Naukri session

Run:

```bash
npm run bootstrap
```

A normal browser window opens. Log into Naukri yourself, complete any OTP/CAPTCHA, open/confirm the profile page, then press Enter in the terminal.

This creates:

`playwright/.auth/state.json`

Do not commit that file.

## 4. Add the GitHub Actions secret

GitHub repository -> Settings -> Secrets and variables -> Actions -> New repository secret.

Name:

`NAUKRI_STORAGE_STATE`

Value:

Base64-encode the contents of `playwright/.auth/state.json`.

On PowerShell:

```powershell
[Convert]::ToBase64String([IO.File]::ReadAllBytes('playwright/.auth/state.json'))
```

On macOS/Linux:

```bash
base64 -w 0 playwright/.auth/state.json
```

Paste the resulting single line into the secret.

## 5. Commit and enable Actions

Push the repository to GitHub. The workflow runs every day at 10:17 AM IST and can also be launched manually from the Actions tab.

The schedule is in `.github/workflows/naukri-update.yml`:

```yaml
on:
  schedule:
    - cron: '17 10 * * *'
      timezone: 'Asia/Kolkata'
  workflow_dispatch:
```

Change `17 10` to your preferred minute/hour.

## 6. If Naukri changes the UI

The script intentionally uses several file-input selectors, with `input[type="file"]` first. If Naukri changes the upload flow, inspect the failed run's `failure.png` artifact and update `src/update-resume.ts`.

## Security notes

- Never put your Naukri username/password in source code.
- Never commit `playwright/.auth/state.json`.
- The authenticated state is stored only as a GitHub Actions secret.
- CAPTCHA and access blocks are treated as failures, not bypassed.
