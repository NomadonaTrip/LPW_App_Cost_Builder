# LPW App Cost Builder

An interactive cost estimate for the Windrow clearing app, prepared by **Orban Forest Inc.** for **Loewen Property Works**.

The customer switches features on or off and sees the price, launch date and trade-offs update. When they are done, they tap **Send to Orban Forest Inc.** and their choices go straight to a Google Form.

Everything is in one file, `index.html`. It has no build step, no dependencies and no server code.

## How it works

1. The customer starts from a preset: **Core only**, **Native MVP** (recommended) or **Full product**.
2. They toggle features, open any feature to read details and what they lose without it, and star the ones that matter most.
3. The floating bar shows the running total. Tapping **Review** opens a summary with the price, HST, launch date, priorities and what was left out.
4. **Notes for Orban Forest** fills itself in with a short summary of their choices:
   - the plan they picked
   - the estimate before and after HST, and the hours
   - the launch date
   - features added to or removed from the Native MVP
   - their starred priorities
5. The customer can add their own comments under the **— Your notes —** line. Their comments stay put when they change selections.
6. **Send to Orban Forest Inc.** posts the whole message to the Google Form. The button turns green and says **Sent ✓**. If they change anything afterwards, it reads **Send updated choices**.
7. If sending fails, the message is copied to the customer's clipboard and they are asked to email it instead.

Selections and notes are saved in the customer's browser, so they can close the page and come back later.

### Example message

```
Windrow app: choices from Loewen Property Works
Plan: Custom, starting from the Native MVP
Estimate: $19,800 + HST ($22,374 with HST), 330 hours
Launch: Launches next season

Added to the MVP:
  + Phone booking line ($900)
  + Route optimization ($4,200)

Removed from the MVP:
  − Refer a friend (saves $840)

Top priorities:
  ★ Status updates

— Your notes —
Can we start Dec 1?
```

## Google Form setup

Responses go to the Google Form **LPW App Cost Builder**, which has one optional **Paragraph** question.

The connection is set near the top of the `<script>` section in `index.html`:

```js
const GFORM = {
  FORM_ACTION: "https://docs.google.com/forms/d/e/<form id>/formResponse",
  MESSAGE_FIELD: "entry.<question id>",
};
```

- `FORM_ACTION` must end in **`/formResponse`**. A link ending in `/viewform` drops every submission without an error.
- `MESSAGE_FIELD` is the question's entry ID. To find it, open the form, choose **⋮ → Get pre-filled link**, type `test` as the answer, click **Get link** and copy the `entry.…` part.

Keep these form settings:

- **Restrict to users in your organization**: off
- **Collect email addresses**: off
- No other **required** questions. The page fills in only one field.
- **Responses → ⋮ → Get email notifications for new responses**: on

Google does not tell the page whether a submission was accepted, so **Sent ✓** shows either way. After any change to the form or to these settings, send a test from the live page and check the **Responses** tab.

## Editing the estimate

All pricing lives in the `<script>` section of `index.html`:

| What | Where |
|---|---|
| Hourly rate, HST, next-season overhead hours | `const RATE = 60, HST = 0.13, S2_OVERHEAD = 40` |
| Features: name, hours, description, "without it" text | `ITEMS` |
| Feature sections | `GROUPS` |
| Launch-date messages and thresholds | `timeline()` |
| Wording of the message sent to the form | `summary()` |
| Prepared date and validity | `<footer>` in the HTML |

Each feature in `ITEMS` has a phase (`ph`):

| Phase | Meaning | In presets |
|---|---|---|
| `core` | Always included and can't be switched off | All |
| `mvp` | Recommended for launch | Native MVP, Full product |
| `step2` | Next season. Adds 40 hours of testing and release overhead and moves the launch to the 2027–28 season | Full product |
| `opt` | Optional extras | None |

`req: ['o7']` means a feature needs another one. Switching it on adds the required feature, and switching the required feature off removes it too.

If you change features or presets, change the storage key `windrow-builder-v3` (for example to `-v4`). Otherwise returning visitors may load old saved selections.

## Hosting

The page must be served from a website for sending to work. Opening the file directly, or viewing it inside a claude.ai artifact, won't send.

- **GitHub Pages:** in this repo, go to **Settings → Pages**, choose **Deploy from a branch**, select the main branch and `/ (root)`, and save. The page is published at `https://nomadonatrip.github.io/LPW_App_Cost_Builder/`.
- **Netlify:** drag the folder onto app.netlify.com/drop.

To test locally:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

## Files

| File | Purpose |
|---|---|
| `index.html` | The Cost Builder page |
| `index.backup.html` | Version from before Google Form sending was added. Git ignores it. |

The estimate is in Canadian dollars at $60 per hour, before app-store accounts, hosting, maps, texts and Stripe fees. It was prepared on November 13, 2026 and is valid for 30 days.
