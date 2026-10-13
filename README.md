# Phonetics Marketplace — mobile-first starter

**Brand:** Phonetics  
**Tagline:** Find your next phone. Love the price.  
**Repository target:** `phonetics-official` (new repository as requested)  
**Frontend:** static HTML/CSS/JS  
**Backend plan:** Supabase free tier + secure Edge Functions  
**Email notifications:** EmailJS using the owner's supplied IDs

## Important status — read first

This ZIP contains a starter frontend and admin UI, SQL schema, and setup guide. It has **not** been connected to a live Supabase project, and EmailJS template variable names/recipient settings have **not** been verified. The secure Edge Functions `submit-seller` and `submit-buyer` are referenced by the frontend but are not included as deployed services in this ZIP. Until those functions and storage policies are created and tested, seller/buyer submissions will show an error and must not be treated as saved. Do not publish this as a complete working marketplace yet.

The admin page uses real Supabase email/password authentication and checks the `admin_users` authorization table. It is not a fake login. Private data must stay behind Row Level Security (RLS).

## Files

- `index.html` — public homepage, search, sell form, buyer request dialog
- `style.css` — responsive navy/white/gold design
- `app.js` — public listing read, form validation and backend calls
- `config.example.js` — safe config template; copy to `config.js`
- `admin/index.html` — separate developer/admin portal
- `admin/admin.js` — admin login and records UI
- `admin/admin.css` — admin styles
- `supabase/schema.sql` — starting database schema and RLS policies
- `.nojekyll` — GitHub Pages helper

## EmailJS values supplied by owner

- Service ID: `service_rf7s8dh`
- Seller template: `template_m4r9o4s`
- Buyer template: `template_68v8194`
- Public key: `js4Cs0f0vvNe9dLON`
- Browser SDK: `https://cdn.jsdelivr.net/npm/@emailjs/browser@4/dist/email.min.js`

Before testing, open the EmailJS dashboard and verify:
1. The service is correct.
2. `template_m4r9o4s` is for seller submission/admin notification.
3. `template_68v8194` is for buyer purchase-request notification.
4. Recipient settings are correct and variables match the actual templates.

Suggested variables were supplied by the owner; they are not assumed to match the live templates. EmailJS sends notifications only; it is not a database or file storage. Email failures must not be mistaken for database failures.

## Recommended free backend: Supabase

Supabase offers PostgreSQL database, Auth, Storage, Row Level Security and Edge Functions. Its free plan has usage limits and terms can change; check the current pricing/limits before production use. You can operate the dashboard from Android Chrome, but configuring SQL, policies, functions and storage on a phone is more difficult than on a computer.

### Android setup outline

1. Create a new Supabase project on the free plan. Keep the database password private.
2. In the Supabase SQL Editor, run `supabase/schema.sql` in the **new project only**.
3. Create Storage buckets:
   - `public-phone-photos`: public read only for approved listing photos; upload restricted to secure server workflow/admins.
   - `private-proofs`: **private** bucket for bills/proof documents; only authorized admins can create short-lived signed links.
4. Create an admin user under Supabase Authentication. Then, while signed in as the project owner in SQL Editor, add the user UUID to `admin_users`:
   ```sql
   insert into public.admin_users (user_id)
   values ('PASTE_THE_AUTH_USER_UUID_HERE');
   ```
   Only do this for the owner/authorized staff. Do not put the UUID in public HTML.
5. Copy `config.example.js` to `config.js`, then replace the project URL and anon/publishable key. Never use a `service_role` key in browser code.
6. Deploy secure Supabase Edge Functions named `submit-seller` and `submit-buyer`. They must validate fields, MIME types and sizes on the server, upload public photos and private proof files to the correct buckets, save private records, and return only safe response data. Keep service credentials in Supabase Function Secrets. The frontend expects these endpoints, but their implementations are not bundled or deployed yet.
7. Add Storage policies so public users cannot browse private proofs or upload arbitrary files. The public photo bucket should expose only approved public listing photos.
8. Verify RLS and policies before accepting real customer information.

### Critical security notes

- Do not remove RLS or make seller/buyer tables publicly readable.
- `seller_submissions` contains private mobile numbers, asking prices, notes and proof paths.
- `buyer_requests` contains personal information and must be admin-only.
- Never expose private proof URLs as permanent public URLs.
- A static website cannot securely hold server secrets. Secure server-side validation and storage are required.
- A GitHub Pages site is static hosting only; it does not provide a private server by itself.

## GitHub upload from Android

1. Open GitHub and create a **new repository** named `phonetics-marketplace` (or use your chosen new name). Do not delete the old repository.
2. Upload the ZIP contents, not the ZIP file itself. The repository root must show `index.html`, `style.css`, `app.js`, `README.md`, `.nojekyll`, and `config.js` after configuration. Keep `admin/` and `supabase/` folders.
3. In Settings → Pages, choose **Deploy from a branch**, branch `main`, folder `/(root)`. Wait for GitHub to publish, then verify the URL GitHub shows.

If you keep the intended name `phonetics-official`, its expected Pages URL is `https://govindsen8103859-gif.github.io/phonetics-official/`; that URL is not guaranteed until repository and Pages settings are confirmed.

## Current testing status

- File list and internal local references: included and reviewed at creation time.
- Responsive styling: CSS includes mobile breakpoints; not device-tested on your phone.
- HTML/JavaScript: not yet validated in a browser.
- Supabase integration: **UNTESTED / NOT CONNECTED**.
- Photo and proof storage: **NOT CONNECTED**.
- Secure Edge Functions: **NOT DEPLOYED**.
- EmailJS templates and delivery: **UNTESTED**.
- Admin login and private-data access: **UNTESTED against a live backend**.
- GitHub Pages deployment: **NOT VERIFIED**.

Do not use this starter for real seller/buyer data until the backend and security rules are fully configured and tested.
