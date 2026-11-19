# Premiere volunteer form — standalone version

The TEST form as a plain web page, so it can live on any ordinary website
instead of inside Google's script service. That is the fix for the phone
error Darve hit Nov 19: when the form is served by Google, a phone signed
into two Google accounts gets the Drive "unable to open the file" error
(the /u/ account-index problem — the plain exec URL returns 200, the
/u/0/ and /u/1/ forms return 404). A normal web page never hits it.

## How it works

- `index.html` — the whole form: same 3-step flow, same CAST theme,
  same phone validation, same remember-me (email in localStorage,
  details looked up from the contact list), plus a "Return to the CAST
  Home Page" button on the confirmation step.
- It talks to the same Apps Script backend over a JSON API (JSONP, so it
  works from any static page with no server code): `?action=getRoles`,
  `?action=lookupContact&email=...`, `?action=submitSignup&name=...&
  email=...&phone=...&roles=...` with `&callback=` for JSONP.
- Backend: `~/workspace/premiere-volunteer-form/Code.gs` (doGet serves
  the API when `?action=` is present, the old HTML form otherwise).
  Signups still land in the same TEST sheet, Counts/Contacts tabs are
  still maintained, Darve is still emailed when a role fills.
- The API base URL is the `API_URL` constant at the top of the script
  in `index.html`. Change that one line to point at a different
  deployment.

## Deploy

1. Deploy the backend: put the local Code.gs into the Apps Script
   project "Premiere Volunteer Form (TEST)" and deploy as a NEW VERSION
   of the existing deployment (same URL — never a new deployment).
2. Verify the API: `curl "<exec-url>?action=getRoles&callback=test"`
   should return `test({"ok":true,"roles":[...]});`.
3. Put `index.html` on any static host (Netlify, Cloudflare Pages, a
   subdomain of a domain Darve already owns — e.g. volunteer.aggfotoz.com
   via one DNS CNAME). No server code, no build step, no cost.
4. Verify on a phone signed into two Google accounts: the page loads,
   roles show with live counts, a signup lands in the TEST sheet.

## Status

- Built Nov 19, 2026. Both files syntax-checked. Backend NOT yet
  redeployed; front end NOT yet hosted — waiting on Darve's hosting
  choice (subdomain volunteer.aggfotoz.com vs Netlify for now).
- The Google-hosted version (Apps Script Index.html, Version 5 themed)
  is still live at the Volunteer Test page until the standalone one
  replaces it.
