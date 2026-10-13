# LFMP Client Onboarding

An internal tool for the LFMP team. You fill it in for a client during onboarding. It asks every question on **Onboarding Form v3 (DRAFT)** (`45mrYxcr6deEfB96nMsH`), in the same order and with the same wording, split into 4 steps.

## Files
| File | Purpose |
|---|---|
| `index.html` | The whole tool (one file, no build step) |
| `worker/worker.js` | The Cloudflare Worker relay that saves to GHL. Deploy it on Cloudflare; GitHub only stores a copy. |
| `SETUP.md` | How to connect to GHL (token, Worker, `RELAY_URL`) |

## Two modes
- **Browser-only** (when `RELAY_URL` in `index.html` is blank): answers are saved in the browser of the computer you use, and nothing goes to GHL.
- **Connected** (when `RELAY_URL` is set; see `SETUP.md`): you search for the client's GHL contact. Existing answers load from GHL. **Finish step** saves your changes to the contact, and **Submit** saves them and adds the tag **`onboarding-form-submitted`**, which triggers the GHL onboarding workflow.

## Exports (admin)
Turn on **Admin mode**, then click **Export** beside a client on the client list: **PDF** (the full submission; password entries are masked), **CSV** or **JSON**. The admin bar also has **All clients (CSV)**.
Admin mode is a switch on the page, not a login.

## Publish on GitHub Pages
1. Create the repo `client-onboarding` in the LFMP-Rockstars organization (Public) and upload `index.html`, `README.md`, `SETUP.md` and the `worker` folder.
2. Go to Settings → Pages → Source: "Deploy from a branch", choose `main` and `/ (root)`, then Save.
3. It's live at `https://lfmp-rockstars.github.io/client-onboarding/`.

## Still to do
- Team passcode (`HUB_PASSCODE` on the Worker) or Cloudflare Access login.
- Review of the password fields.
- Source-form fixes in GHL (see `claude/Onboarding_Hub_Alignment_Review.md` in the project).
