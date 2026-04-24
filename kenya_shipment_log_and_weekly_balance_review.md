Update the Kenya fuel-tracking workbook and markdown for today using the most recent public information available.

## Core principle
Do not calculate directly at weekly level.
Use an internal DAILY product ledger for:
- Petrol (PMS)
- Diesel (AGO)
- DPK / Jet (if available)

Then publish the weekly table as a Sunday snapshot of that daily ledger.

## Outputs
Create or overwrite:
1. A Markdown file
2. An Excel file with exactly three sheets:
   - Shipment_Log
   - Weekly_Balance
   - Announcements

## Source priority
Use sources in this order:

### 1) Official records
- KPA FourteenDaysList
- KPA ShippingMovement
- EPRA
- KPC
- CBK
- National Treasury
- Parliament of Kenya
- State Department for Petroleum
- UNOC

### 2) International / industry
- Reuters
- Argus Media
- Xinhua
- MNI
- MarineTraffic
- VesselFinder

### 3) Kenyan / regional media
- Nation
- The Star
- Citizen
- Eastleigh Voice
- KBC
- NTV
- People Daily
- Capital FM
- Kenyan Wallstreet
- CNBC Africa
- Standard
- Business Daily
- New Vision
- Streamline
- The Kenya Times
- The Trading Room

## Internal daily-ledger rules
For each product and each calendar day, calculate:

Closing_Stock[t] = Closing_Stock[t-1] + Accepted_Inflows[t] - Burn[t]

Do not publish this daily ledger unless needed, but use it to generate all weekly balances.

## Inflow-credit rules
Classify each vessel/programme as one of:
- Scheduled only
- Arrived / berthed
- Discharged / market-accepted
- Disputed / rejected / excluded

Rules:
- Scheduled only: log in Shipment_Log, but do not automatically give full stock credit
- Arrived / berthed: give either zero credit or staged partial credit over discharge window
- Discharged / market-accepted: give full stock credit
- Disputed / rejected cargo: keep row in Shipment_Log, but modeled credit = 0.0 ML unless acceptance into the Kenyan market is clearly confirmed

Never double-count the same cargo when it appears in both KPA and media.

## Conversion rules
Use product-specific tonne-to-ML conversion factors.
Do not use one generic conversion factor for all products.

## Burn-rate rules
Estimate burn using two layers:
1. Baseline historical burn from recent official demand / throughput assumptions
2. Official-implied burn when a public announcement provides both stock and days of cover

Formula:
Implied daily burn = official stock / official days of cover

Use a weighted blend of baseline burn and latest official-implied burn.

## Shipment_Log rules
Keep all prior rows unless the user explicitly instructs a reversal or removal.
Only add:
- newly identified incoming vessels
- newly confirmed programme rows
- newly relevant non-PMS/AGO rows for context

Columns:
- ETA / event date
- Vessel / programme
- Product
- Public quantity
- Modeled credit (ML)
- How used in model
- Public record used

## Weekly_Balance rules
Preserve all prior week-ending rows.
Use the internal daily ledger to recompute weekly balances only when:
- a new vessel is added,
- a prior vessel is explicitly removed,
- or an official rebase anchor is applied.

Include a column:
- Official announcement logic / gap

For any row touched by an official announcement, write:
- Validation-only or Rebase anchor
- Announcement date
- Reserve date
- Days from reserve date to week-ending date
- Days from announcement date to week-ending date
- Official petrol and diesel in comparable ML
- Model petrol and diesel on the reserve date
- Delta = Actual - Model
- Whether the model was reset or left unchanged
- If the weekly row only carries forward a rebase anchor, say:
  "Not an independent validation; carried forward from reserve-date anchor."

Important:
Compare official quantities to the model on the RESERVE DATE, not the publication date.

## Rebase rules
A public announcement may be used in one of two ways:

### Validation only
Use when:
- quantified official source exists
- but stock perimeter may differ
- or the source is not strong enough to reset the model

### Rebase anchor
Use only when all are true:
- official source
- explicit reserve date
- product-specific quantities
- stock perimeter reasonably comparable to the model
- strong enough to reset the running balance

When rebasing:
- keep the raw model path
- reset the rebased model at the reserve date
- roll forward again using post-checkpoint accepted inflows and burn

Do not silently overwrite the raw model.

## Announcements sheet
Create one sheet named Announcements with columns:
- Announcement date
- Reserve date stated
- Official / source of statement
- Petrol announced
- Diesel announced
- Kerosene / jet / DPK announced
- Unit / comparability note
- Remarks
- Predicted petrol on reserve date (ML)
- Actual petrol comparable (ML)
- Delta petrol (Actual - Predicted)
- Predicted diesel on reserve date (ML)
- Actual diesel comparable (ML)
- Delta diesel (Actual - Predicted)
- Model handling / logic
- Source URLs

For unquantified announcements:
- leave actual/prediction/delta numeric cells blank
- mark:
  "Unquantified announcement. Used for context only; no numeric comparison or rebase possible."

## Date rule
Always separate:
- Announcement date
- Reserve date
- Week-ending bucket date

Never treat them as identical unless the source explicitly says they are the same.

## Markdown file structure
The Markdown file must contain exactly these sections:
1. Shipment_Log
2. Weekly_Balance
3. Announcements

At the top, add short method notes explaining:
- reserve date vs announcement date
- validation-only vs rebase
- product-specific conversion factors
- which official checkpoint(s) currently anchor the rebased model

## If no new vessels are found
- keep the existing rows unchanged
- update notes and announcement comparisons only
- state clearly:
  "No new official PMS/AGO vessel rows were added today."

## Citation rules
- Include source URLs in the files
- Prefer official URLs and top-tier reporting in narrative notes
- If Business Daily or Standard have relevant new articles, add them to notes and source lists
