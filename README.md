# Marin Lab Prices

An interactive comparison of what 27 common lab tests cost in Marin County depending on who is paying — Medicare, commercial insurance, MarinHealth Medical Center's posted cash and list prices, and Direct Primary Care (DPC) wholesale rates.

> **Live tool:** _add URL once deployed_

## Why this exists

The price you pay for a routine blood test depends entirely on who is paying. A complete blood count (CBC) costs $2.55 at wholesale through a DPC clinic and $394 at MarinHealth's posted list price — a 154× markup. Insured patients rarely see those numbers because their plan negotiates a different rate behind the scenes, but uninsured patients, those between plans, and patients on high-deductible plans before they hit their deductible generally pay the posted price.

This tool makes those differences visible side-by-side so patients can navigate them.

## What's in the tool

- **Scenario picker** — pick how you'd be paying (DPC, Medicare, with insurance, no insurance) and watch the basket price change
- **Build-your-basket UI** — start from common presets (annual checkup, diabetes follow-up, thyroid panel, STI screen, iron studies) or pick individual tests
- **"What can go wrong with insurance" panel** — live calculations showing the dollar impact of common situations that change what an insured patient actually pays (deductible not met, out-of-network labs, denied claims, prior-auth refusals)
- **Per-test comparison table** — all 27 tests, sortable by any column, with a click-through detail view that shows the full distribution of commercial insurance negotiated rates
- **CSV export and shareable URLs** — the basket state is encoded in the URL hash

## Data sources

| Tier | Source |
|---|---|
| MarinHealth gross, cash, and commercial negotiated rates | [MarinHealth Medical Center Pricing Transparency](https://www.mymarinhealth.org/patients-visitors/medical-center-patients-visitors/billing-insurance/pricing-transparency/) — machine-readable file v3.0.0, updated 2026-03-26 |
| Medicare | 2025 CMS Clinical Lab Fee Schedule (cross-referenced against the same MRF) |
| DPC wholesale | Public price lists from Iron DPC, Birmingham DPC, Simplicity DPC, and Access Med Labs (April 2026) |

## Methodology notes

- Hospital cash and list prices use the **modal** (most common) chargemaster row per CPT code, which matches the prices shown by MarinHealth's own consumer pricing tool. Outlier rows are excluded.
- Cash price is consistently 60% of list price (40% self-pay discount applied at time of service).
- Commercial insurance min, median, mean, and max exclude a $2,330 default contract ceiling and a few sub-$5 Medicare-anchored rows that aren't real commercial pricing.
- Chlamydia and Gonorrhea NAATs are billed separately at MarinHealth ($318 each) but typically ordered together; the DPC bundle price of $15 is split as $7.50 each.
- Flu A/B Rapid is excluded — it isn't in MarinHealth's MRF.

The full methodology is also reproduced in the "About this tool" section of the page itself.

## Tech stack

A single static HTML file with no build step.

- HTML, CSS, and vanilla JavaScript
- [Chart.js](https://www.chartjs.org/) v4.4 (loaded via CDN)
- All test data is embedded in `index.html` as a JS constant

## Running locally

Open it directly:

```sh
open index.html
```

Or serve over HTTP:

```sh
python3 -m http.server 8000
# then visit http://localhost:8000
```

## Deploying

Any static host works:

- **Netlify** — drag `index.html` onto [app.netlify.com/drop](https://app.netlify.com/drop)
- **Cloudflare Pages** — connect this repo, leave build command empty, output directory `/`
- **GitHub Pages** — Settings → Pages → deploy from the `main` branch
- **Squarespace embed** — host it on one of the above, then add an Embed Block with an iframe pointing at it

## Updating the data

The dataset is a single `TESTS` array near the top of the `<script>` block in `index.html`. Each entry has these fields:

```js
{
  name: "CBC w/ Differential",
  cpt: "85025",
  cat: "Hematology",
  medicare: 7.77,
  insN: 180,            // number of commercial contracts in the MRF
  insMin, insMedian, insMean, insMax,
  gross: 394,           // hospital list price (modal chargemaster row)
  cash: 236.4,          // 60% of gross
  dpc: 2.55,
  dpcSrc: "Iron DPC",
}
```

When MarinHealth publishes a new MRF, regenerate the values from the new CSV using the modal-row logic described in the Methodology section above.

## Disclaimer

This tool is for informational purposes only and is not medical, legal, or financial advice. Real prices for an individual patient depend on insurance coverage, contract terms, place of service, modifiers, and other factors. Always confirm pricing directly with your provider and insurance plan before scheduling care.

## License

MIT. The code is freely reusable. The underlying pricing data is sourced from publicly published documents (cited above) and is not owned by this project.
