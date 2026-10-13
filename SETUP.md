# Connect the Onboarding Hub to GHL (about 15 minutes)

The hub page (GitHub Pages) talks to a small **Cloudflare Worker** (the "relay"). The relay holds the GHL token and saves answers to the contact. When the team clicks **Submit**, the relay adds this tag to the contact:

> **`onboarding-form-submitted`**

Use that tag as the trigger in your GHL onboarding workflow: **Contact Tag Added → onboarding-form-submitted**.

---

## 1. Create a GHL Private Integration token
In the **LFMP sub-account**: Settings → Private Integrations → **Create new integration**
- Name: `Onboarding Hub relay`
- Scopes: **View Contacts**, **Edit Contacts**, **View Custom Fields** (contacts.readonly, contacts.write, locations/customFields.readonly)
- Copy the token. You'll paste it into Cloudflare only; don't put it in GitHub or in the page.

Also note the **Location ID**: Settings → Business Profile (or it's in the sub-account URL: `/location/<ID>/...`).

## 2. Create the Worker
1. Log in at dash.cloudflare.com (a free account is fine) → **Workers & Pages** → **Create** → **Create Worker**.
2. Name it `client-onboarding` → **Deploy** → **Edit code**.
3. Delete the sample code, paste the contents of `worker/worker.js`, then **Deploy**.
4. Go to **Settings → Variables and Secrets** and add:

| Name | Type | Value |
|---|---|---|
| `GHL_TOKEN` | **Secret** | the token from step 1 |
| `GHL_LOCATION_ID` | Text | the LFMP location ID |
| `ALLOWED_ORIGIN` | Text | `https://lfmp-rockstars.github.io` |
| `HUB_PASSCODE` | **Secret** | *(recommended)* a team passcode. Leave it out to run without one. |

5. Copy the Worker URL, for example `https://client-onboarding.lfmp.workers.dev`.

## 3. Point the hub at the Worker
In `index.html`, find this line (search for `RELAY_URL`):
```js
const RELAY_URL = "";
```
Paste the Worker URL between the quotes, then upload `index.html` to GitHub again (replacing the old one).

## 4. Test with a test contact
1. Open the hub and search for a **test** contact. Enter the passcode if you set one.
2. Fill a page or two → **Finish step** → check the contact in GHL. The answers should be in the custom fields.
3. Go to Step 4 → **Submit & Book Your Onboarding Call** → **Submit to GHL**. The contact gets the tag `onboarding-form-submitted`.
4. Confirm your workflow fires on that tag.

## How saving works
- Opening a contact loads its existing answers from GHL.
- Edits are kept in the browser as "unsaved changes" until **Finish step**, **Save to GHL now** or **Submit**.
- Only fields you changed are sent, so other fields on the contact are untouched.
- Submit with open items asks for a second click to confirm.
- Fields the relay can't save are listed in the browser console (F12 → Console). This happens when a custom field key doesn't exist in GHL, or when Country isn't a 2-letter code like `US`.

## Security notes
- Without `HUB_PASSCODE`, anyone who finds the relay URL can search and edit contacts. Set a passcode, or add Cloudflare Access, before wider use.
- The token lives only in Cloudflare. To revoke access, delete the Private Integration in GHL.
