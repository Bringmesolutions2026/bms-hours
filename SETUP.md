# BMS Hours App v2 — Setup Guide

## Files

| File | Purpose |
|------|---------|
| `index.html` | Employee app — clock in/out with selfie + GPS |
| `dashboard.html` | Management dashboard — live status, shifts, scores, alerts |
| `google-apps-script.js` | Backend — runs in Google Apps Script |

## Deploy Steps

### 1. Update Google Apps Script

1. Open your BMS Google Spreadsheet
2. Go to **Extensions > Apps Script**
3. Delete all existing code
4. Paste the entire contents of `google-apps-script.js`
5. Click **Deploy > Manage deployments > Edit (pencil icon)**
6. Change version to **New version**
7. Click **Deploy**
8. Copy the Web App URL

### 2. Update URLs in HTML files

In both `index.html` and `dashboard.html`, find this line:

```
var SCRIPT_URL = 'https://script.google.com/macros/s/AKfycby.../exec';
```

Replace with your actual deployed Web App URL (same URL for both files).

### 3. Set up the daily trigger

In Apps Script, run the function `setupTriggers()` once:

1. In the Apps Script editor, select `setupTriggers` from the function dropdown
2. Click **Run**
3. Approve permissions when prompted

This creates a daily 10pm check for missing clock-outs.

### 4. Host the HTML files

Options:
- **GitHub Pages** (free) — push to a repo, enable Pages
- **Google Sites** — embed via iframe
- **Any static host** — Netlify, Vercel, etc.
- **Direct file** — open from phone browser (works for testing)

### 5. Add to phone home screen

On employee phones:
1. Open the employee app URL in Safari/Chrome
2. Tap Share > Add to Home Screen
3. Name it "BMS Hours"

## Google Sheets Structure

The backend automatically creates these sheets:

| Sheet | Purpose |
|-------|---------|
| **Shifts** | Every clock-in/out with full audit trail |
| **GPS Audit** | Start/finish GPS, distances, flags |
| **Reliability** | Every score event with running totals |
| **Employees** | Current status, scores, bonus tiers |
| **Alerts** | GPS anomalies, missing clocks, long shifts |
| **Payroll Export** | Clean payroll data for Xero |

## Depot GPS Coordinates

Currently configured (in `index.html`):

| Depot | Latitude | Longitude |
|-------|----------|-----------|
| Oxley | -27.6068 | 152.9782 |
| Carindale | -27.5052 | 153.1000 |
| Loganholme | -27.6869 | 153.2027 |
| Macgregor | -27.5639 | 153.0680 |

GPS radius for clock-in validation: **2km**

Update these in the `DEPOTS` object in `index.html` if locations change.

## Lunch Break Rule

- Shifts > 5 hours: automatic 30-minute unpaid deduction
- Shifts ≤ 5 hours: no deduction
- Displayed on shift summary and payroll export

## Reliability Scoring

Starting score: **100 points**

| Event | Points |
|-------|--------|
| Correct clock-out | +1 |
| Full week attendance | +5 |
| Accepted extra shift | +3 |
| Positive customer feedback | +5 |
| No damage incidents (month) | +10 |
| Late start | -5 |
| Missing clock-in | -10 |
| Missing clock-out | -10 |
| Unapproved absence | -20 |
| Customer complaint | -10 |
| Damage incident | -15 |
| No-show | -50 |
| Manual correction needed | -5 |
| False finish location | -10 |
| Repeated GPS discrepancies | -20 |

## Bonus Tiers

| Score | Tier | Monthly Bonus |
|-------|------|---------------|
| 120+ | Elite | $300 |
| 110–119 | Gold | $200 |
| 100–109 | Silver | $100 |
| Below 100 | None | $0 |

## Future Modules (database ready)

The employee profile + shift structure supports:
- Xero Payroll integration (Payroll Export sheet)
- Crew profitability tracking (via shift + revenue data)
- Revenue per truck (add truck field to shifts)
- Damage tracking (via reliability events)
- Vehicle inspections (new sheet, linked to employee)
- Customer feedback scores (via reliability events)
