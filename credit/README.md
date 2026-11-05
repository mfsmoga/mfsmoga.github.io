# Petrol Pump Credit Manager: setup guide

One app for **Android, iPhone, Windows/Mac and any web browser**. Data is stored in your own **Supabase** database (free tier), so the owner's phone, staff phones and the office PC all see the same live data.

## What's in this folder

| File | Purpose |
|---|---|
| `index.html` | The whole app |
| `schema.sql` | Database tables, owner/staff permissions and approval rules |
| `manifest.webmanifest`, `sw.js`, `icon-*.png` | Let the app install on phones and PCs like a normal app |

---

## Step 1: Create the database (10 minutes, one time)

1. Go to **supabase.com**, sign up, and click **New project**. Choose region **Mumbai (ap-south-1)** and set a database password.
2. Open **SQL Editor → New query**, paste the full contents of `schema.sql`, and click **Run**. It should say *Success*.
3. Open **Authentication → Sign In / Providers → Email** and make sure Email is enabled.
   *Recommended:* turn **off** "Confirm email" so staff can log in right after signing up.
4. Open **Project Settings → API** and copy these two values:
   - **Project URL** (`https://xxxx.supabase.co`)
   - **anon public** key (it is safe to put in the app; security comes from the database rules)

## Step 2: Put your keys in the app

Open `index.html` in any text editor (Notepad works) and fill in this line near the top:

```js
const CONFIG = { SUPABASE_URL: 'https://xxxx.supabase.co', SUPABASE_ANON_KEY: 'eyJhbGciOi...' };
```

(If you skip this, the app asks for the keys on first launch, but then every device has to enter them.)

## Step 3: Put it online (free)

The easiest option is **Netlify Drop**: go to app.netlify.com/drop and drag this whole folder onto the page. You get a link like `https://pump-credit.netlify.app`.
Other options that work: GitHub Pages, Vercel or Cloudflare Pages. The site must use **https** for the app to install.

## Step 4: First login

1. Open your link, tap **Create account** and sign up. **The first account becomes the Owner.**
2. Each staff member opens the same link, taps **Create account** and signs up with their own email.
3. The Owner goes to **Staff** and switches each staff member to **Active**. Until then they cannot see anything.

## Step 5: Install on every device

- **Android (Chrome):** open the link, tap the ⋮ menu, then **Install app** (or *Add to Home screen*).
- **iPhone (Safari):** tap Share, then **Add to Home Screen**.
- **Windows / Mac (Chrome or Edge):** click the install icon in the address bar.

## Daily use

| Who | What |
|---|---|
| Owner (each morning) | **Update rates**: today's petrol and diesel price |
| Staff | **Credit slip**: pick the customer and vehicle, type litres or ₹, and save. The vehicle is filled right away and the slip shows *Pending* |
| Staff | **Payment**: cash, UPI, cheque or bank transfer, with the reference number. It also shows *Pending* |
| Owner | **Approvals**: approve entries one by one or tick *Select all*, or reject with a reason. Updates arrive live |
| Owner | **Bills**: customers whose cycle has ended appear under *Bills ready to generate*. Tap **Generate all**, then open each bill and **Print/PDF** or **WhatsApp** it |
| Owner | **Customer → Send reminder**: sends a WhatsApp message in the customer's own language (Hindi, Punjabi or English) |

## How the rules work

- **Approvals:** staff entries are always saved as *Pending*. This is enforced by the database itself, not just the app, so it can't be bypassed. Only the Owner can approve, reject, edit customers, set rates or make bills. Nothing is ever deleted; rejected entries stay in the history.
- **Balances:** the *approved balance* counts approved entries only. *Pending* entries are shown separately as provisional, and the credit-limit warning counts both.
- **Billing cycles:** each customer can be weekly, fortnightly, monthly, custom (any number of days) or seasonal/harvest. Seasonal customers are billed manually from their customer page with **Generate bill**.
- **What goes on a bill:** the previous bill's amount payable, plus all approved entries not yet billed (including late-approved older slips), minus payments. Interest is added at the customer's % per month when the previous bill wasn't paid by its due date, and is shown on the bill.
- **GST:** petrol and diesel are outside GST. Lubricant prices include GST, and the bill shows the taxable value plus CGST/SGST.
- **Backup:** use **Settings → Download backup** for a JSON copy. Supabase also keeps its own backups on paid plans.

## Try it without setup

Open `index.html` and choose **Try the demo**. Sample data is stored only in that browser. Use the yellow bar at the top to switch between Owner and Staff.

## Planned for later (v2)

- Customer login to view their own ledger. The database is ready for a `customer` role.
- Automatic WhatsApp sending through the WhatsApp Business API. v1 opens WhatsApp with the message ready to send.
- Play Store / App Store builds through Capacitor, using the same code.
