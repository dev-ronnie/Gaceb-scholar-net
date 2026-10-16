# GACEB Scholar — simple GitHub Pages edition

This is a plain HTML/CSS/JavaScript website. It has no React build step, npm install, or build command, which avoids the white-screen issue caused by publishing React source files directly.

## 1. Upload it to GitHub (phone-friendly)
1. Download and extract `GACEB_Scholar_Professional.zip` on your phone.
2. Open your existing repository: `Gaceb-e-scholar-platform`.
3. Use **Add file → Upload files** and upload the files inside the extracted folder into the repository root. Do not upload the ZIP itself.
4. Preserve the `assets` folder and its image files. The upload should place `index.html`, `styles.css`, `app.js`, `config.js`, `supabase-schema.sql`, and `SETUP.md` at the top level.
5. Commit the upload. If GitHub cannot replace files or upload a folder on mobile, stop and use GitHub's web upload from a computer rather than deleting files blindly.
6. In **Settings → Pages**, choose **Deploy from a branch**, branch `main`, folder `/(root)`. Wait for the deployment to finish, then reload the site. The page should render even before the database is configured.

## 2. Connect real student accounts, database, PDF uploads and coins
These require a Supabase project; GitHub Pages by itself is only static hosting.
1. Create a project at https://supabase.com/ and wait for it to finish provisioning.
2. In Supabase, open **SQL Editor → New query**, paste the full contents of `supabase-schema.sql`, and run it.
3. Open **Project Settings → API** (or API Keys). Copy the Project URL and the **publishable/anon public key**. Never use a `service_role` or secret key in a browser.
4. Open `config.js` in GitHub and replace `https://YOUR-PROJECT.supabase.co` and `YOUR-SUPABASE-ANON-KEY` with your own Project URL and public key. Save/commit.
5. In Supabase **Authentication → URL Configuration**, add your published GitHub Pages address as a Site URL and as an allowed Redirect URL. For this repo it will usually be `https://dev-ronnie.github.io/Gaceb-e-scholar-platform/`.
6. In Supabase **Authentication → Providers → Email**, decide whether to require email confirmation. If enabled, students must confirm their email before they can log in.
7. Sign up with your own email on the website. Then, in Supabase SQL Editor, promote your own account to admin with:
   `update public.profiles set role='admin' where email='YOUR-EMAIL-ADDRESS';`
   Replace the email with your login email. Run this only for your own trusted administrator account. Refresh/log in again. The Admin tables button will appear in your account panel.

The schema creates tables for profiles, PDF resources, coin transactions, referrals, subscriptions and contact messages. Row-level security protects student records. Students can see their own profile and coin history; only an admin can see the full student table and approve PDFs. An approved PDF upload awards 5 coins once; verified referrals award 5 coins once. The admin must verify referrals after checking they are genuine. Do not promote other users to admin unless you trust them.

## 3. Real PDF storage
The SQL creates a private `study-pdfs` bucket, limits PDFs to 15 MB and sets storage access rules. Students must be logged in to upload. Uploads enter `pending` status and are not shown to everyone until approved by an admin. Approved PDF downloads use short-lived signed links.

## 4. Payments and donations (important)
The site displays the agreed premium prices: UGX 10,000 weekly and UGX 30,000 monthly. **Payments are not live just by uploading the ZIP.** To collect money, GACEB must open and verify a merchant account with a provider that supports the desired Uganda payment methods (for example Flutterwave or Pesapal), configure hosted checkout/payment links, and add them to `config.js` as `WEEKLY_PAYMENT_URL`, `MONTHLY_PAYMENT_URL`, and `DONATION_URL`.

For a secure production subscription, a server-side webhook/Edge Function must verify each provider transaction before changing `subscriptions.status` or granting premium access. Do not activate premium based only on a browser redirect, and do not put payment secret keys, API secrets or mobile-money PINs in `config.js`. The front-end deliberately does not pretend that an unverified payment succeeded. Contact GACEB's payment provider for current onboarding, merchant approval and webhook details before accepting payments.

## 5. Gauge Assistant
Gauge Assistant currently provides helpful built-in study guidance and revision tips. Connecting a real AI model requires a server-side function and a private API key; do not put an AI secret key in browser JavaScript.

## 6. Real images
The `assets` folder includes the GACEB badge/logo and the real founder/teacher-award photos available in the current working files. The original badge image is used without changing its details.

## Troubleshooting
- **White page:** ensure `index.html` and `styles.css` are at the repository root, and the `assets` folder is alongside them. Check spelling/capitalization. Clear/reload the browser cache after deployment.
- **Page works but login/resources say setup required:** that is expected until the Supabase URL/key are added and the SQL schema is run.
- **Email confirmation fails:** add the exact published site URL to Supabase Auth's allowed redirect URLs.
- **PDF upload fails:** check the private bucket exists, the student is logged in, and the file is a PDF no larger than 15 MB.
- **Admin table missing:** confirm your own profile has `role='admin'`, then sign out and back in.

This package is a practical deployable starter with working front-end flows and Supabase integration. Payment activation and live AI are intentionally gated until the secure provider/backend credentials are configured.
