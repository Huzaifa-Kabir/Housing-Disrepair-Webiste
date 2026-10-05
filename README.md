# Tenants Aid

Housing disrepair enquiry site. Static front-end (`public/index.html`, Tailwind via CDN, vanilla JS) plus an Express API that saves each enquiry to Google Sheets and emails the admin.

## Run locally
```bash
npm install
cp .env.example .env      # then fill in the values
npm start                 # http://localhost:5000
```

## Google Sheets setup
1. Google Cloud Console > create project **Tenants Aid** > APIs & Services > Library > enable **Google Sheets API**.
2. Credentials > Create credentials > Service account > open it > Keys > Add key > JSON. Save as `credentials.json` next to `server.js`.
3. Create a Google Sheet. Row 1 headings (A to I):
   `Timestamp | Name | Phone | Email | Postcode | Facing issues | Issues | Landlord | Status`
4. Share the sheet with the `client_email` from `credentials.json` as **Editor**.
5. Copy the sheet ID from the URL (`/spreadsheets/d/<ID>/edit`) into `GOOGLE_SHEET_ID`.
6. If your tab is not called `Sheet1`, set `SHEET_RANGE`, e.g. `Enquiries!A:I`.

## Email alerts
Turn on 2-step verification on the sending Gmail account, create an **App password**, and put it in `EMAIL_PASS`. Alerts go to `ADMIN_EMAIL`. If email fails the lead is still saved to the sheet.

## Deploy (Render, Railway or Heroku)
- Push to GitHub, create a Node web service: build `npm install`, start `npm start`.
- Add the `.env` values as environment variables.
- Do not upload `credentials.json`. Paste its contents, on one line, into `GOOGLE_CREDENTIALS_JSON`.
- If the front-end is hosted elsewhere, set `API_URL` in `index.html` to the full API address and add that site to `ALLOWED_ORIGINS`.

## What the API does
`POST /api/enquiries` with JSON `{ facingIssues, issues[], landlord, name, phone, email, postcode, consent }`
- validates and cleans every field (UK phone and postcode formats, allowed issue and landlord values)
- rate limits to 10 submissions per IP per 15 minutes and drops honeypot spam
- appends a row to Google Sheets and emails a styled alert
- returns `{ "success": true, "message": "Enquiry submitted successfully" }`

## Before going live
- Replace the placeholder image tiles with real photos.
- Add your company registration and regulatory details, and a privacy policy (you are collecting personal data under UK GDPR; register with the ICO if required).
- Check claims-advertising rules for your regulator (SRA or CMC) and review the page copy.
