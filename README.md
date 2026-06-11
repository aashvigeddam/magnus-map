[MAGNUS_MAP_PROJECT_OVERVIEW.md](https://github.com/user-attachments/files/28842800/MAGNUS_MAP_PROJECT_OVERVIEW.md)
# Magnus Map — Project Overview

**Magnus Map is an internal operations dashboard that helps Magnus United map verified
needs, compare them against inventory, identify resource gaps, and decide whether to ship,
fundraise, collect donations, or research partner support.**

> Status: local prototype. The current seed data uses **public-source country need
> profiles** and **real nonprofit support/resource programs** as research-based entries.
> Nothing here represents a confirmed Magnus United partnership, contact, approval, or
> shipment. Entries are labeled **Researching**, **Needs verification**, **Not yet
> contacted**, or **Not a confirmed Magnus partner**.

---

## Core Problem

Magnus United currently relies on tools like Google Sheets, Google Forms, and email. Those
tools do not easily answer:

- What is needed most?
- Where is it needed?
- What does Magnus already have?
- What is missing?
- Should Magnus ship, fundraise, collect donations, or find partner support?
- Which companies or nonprofit programs could help with software, funding, logistics, or
  technology?

The core problem: **Magnus should not send resources randomly based only on what is
available. It should act based on verified needs and clear gaps.**

---

## Core Product Flow

```
Need → Inventory Check → Gap → Priority → Action Plan → Partner / Funding Support
```

Magnus Map turns scattered information (sheets, forms, email) into a clearer
decision-making system: needs are recorded, compared against what's on hand, ranked by a
transparent priority score, and converted into concrete next actions — with partner and
funding research tracked alongside.

---

## Tech Stack

The runnable application is built with:

- **Python**
- **Streamlit**
- **pandas**
- **CSV files** for local storage
- A **local, file-based prototype** — no external APIs, no authentication, no database, no
  cloud, no AI services

> A second, self-contained **interactive HTML version** (`Magnus Map.html`) also exists for
> click-through demos in the browser; it stores data in the browser's local storage and
> mirrors the same pages, logic, and design. It does not use any external API or backend
> either.

---

## How to Run

From the project folder:

```bash
python -m streamlit run magnus_map.py
```

On first run, Magnus Map creates the `data/` folder (if missing) and seeds the CSV files
with the public-source research profiles described below. Existing CSV files are not
overwritten except when you submit or edit an entry in the app.

---

## File Structure

```text
magnus_map/
├── magnus_map.py                     # Streamlit application (runnable)
├── DESIGN_AND_PRODUCT_BRIEF.md       # product + design brief
├── MAGNUS_MAP_PROJECT_OVERVIEW.md    # this document
│
├── Magnus Map.html                   # self-contained interactive HTML version (browser demo)
├── app.css                           # styles for the HTML version
├── engine.js                         # data model + business logic (HTML version)
├── components.jsx                    # shared UI components (HTML version)
├── dashboard.jsx                     # Dashboard page (HTML version)
├── pages-data.jsx                    # Partner Needs + Inventory pages
├── pages-intel.jsx                   # Gap Analysis + Action Plan pages
├── pages-network.jsx                 # Partner Finder + Funding & Resources pages
├── main.jsx                          # app shell / sidebar navigation
├── globe.js                          # CSS/SVG rotating-globe markup (hero visual)
│
├── assets/
│   ├── magnuspfp.png                 # Magnus United logo
│   ├── worldmap.png                  # equirectangular texture for the rotating globe
│   └── earth.png                     # still Earth image (earlier hero iteration)
│
└── data/
    ├── partner_needs.csv
    ├── inventory.csv
    ├── partners.csv
    ├── funding_sources.csv
    └── action_plan.csv
```

> Note on the hero visual: the rotating Earth is rendered **in code** (a flat world-map
> texture wrapped onto a shaded sphere via CSS/SVG — see `globe.js` and `worldmap.png`). The
> project does **not** ship an animated `earth.gif`; `assets/earth.png` is a still image
> retained from an earlier iteration.

---

## Product Pages

### Dashboard

Summarizes the most important operational information:

- A **hero section** with the Magnus United logo and a code-rendered **rotating Earth**
  visual, plus the headline "Map the need. Close the gap."
- **Preview metric cards** — Top Need, Inventory Match (shows *Pending* when there is no
  verified inventory match), Resource Support (count of programs to research), and Partner
  Support (count of programs being researched).
- A **key-metrics strip** — Open Needs, High Urgency, Needs Verification, Resource Programs,
  Partner Programs.
- A **Top Needs** table (ranked by priority score) and a **Public-Source Prototype Feed**
  showing example activity that is explicitly labeled as public-source, not real Magnus
  action.
- A **Next Actions** preview and a **How Magnus Map Works** step-by-step section.

### Partner Needs

Records country / partner / location needs. Fields:

- Need ID
- Date Added
- Partner / Location Name
- Country or Region
- Type of Partner
- Resource Category
- Specific Item Needed
- Quantity Needed *(may be TBD)*
- Urgency
- Deadline *(may be TBD)*
- Who It Helps
- What Happens If Not Filled
- Can Accept Physical Shipments?
- Would Money Help More Than Physical Goods?
- Source Type
- Source Name
- Source Link
- Verification Status
- Notes

Quantities and deadlines can be left as **TBD** — the app does not force invented numbers.
Public-source profiles stay at **Verification Status: Researching** until confirmed.

### Inventory

Tracks what Magnus currently has available. Fields:

- Inventory ID
- Date Added
- Resource Category
- Specific Item
- Quantity Available
- Condition
- Storage Location
- Source
- Estimated Value
- Notes

The prototype ships with **no seeded inventory**, so nothing reads as "Ready to Ship" until
real stock is entered.

### Gap Analysis

Compares needs against inventory:

- **Matching** by resource category and specific item name (case-insensitive, exact text).
- **Quantity available** is summed from matching inventory.
- **Gap = Quantity Needed − Quantity Available** when numeric data exists.
- **TBD handling:** when quantity is unknown, the app does **not** invent a numeric gap — it
  shows **Gap: needs verification** and **Status: Needs verification**.
- **Statuses:** Ready to Ship, Partially Filled, No Inventory, Needs Funding, Needs Partner
  Support, and Needs Verification.
- **Priority score (1–100):** urgency + deadline proximity + need type + gap size, capped at
  100. Gap points are only added when a numeric quantity is known.
- **Recommended next step** is derived from the status (e.g. Needs Verification →
  "Verify quantity, receiving partner, and whether money or physical goods are preferred").

### Action Plan

Turns gaps and research items into tasks. Action types:

- Research
- Contact Partner
- Fundraise
- Run Donation Drive
- Ship — **only** generated when there is verified inventory and a verified receiving
  partner

Because all current profiles have TBD quantities and there is no seeded inventory, the app
generates **Research** ("verify…") actions and **no shipping actions**. Generation is
de-duplicated by (need + action type); owner, status, due date, and notes you edit are not
overwritten.

### Partner Finder

A **manual** research database of potential partner / resource organizations:

- It does **not** automatically invent real partners.
- It does **not** claim any organization is a confirmed Magnus partner.
- Current company/program entries are labeled **Researching** until the user changes them.
- Includes a "find partners for a need" matcher that ranks **only manually-entered**
  partners by resource match → region match → reliability → recency.

### Funding & Resources

Tracks nonprofit support programs, software tools, funding sources, and resource
opportunities. Current research entries:

- Google Ad Grants
- Microsoft for Nonprofits
- Canva for Nonprofits
- TechSoup
- Salesforce Power of Us
- Slack for Nonprofits
- OpenAI for Nonprofits
- Zoom Cares / Zoom for Nonprofits
- Cisco Technology Grant Program
- HP Foundation / HP Accelerator
- Notion for Nonprofits
- DHL humanitarian logistics partnership model

All are marked **Researching** with eligibility notes that state eligibility is required and
not assumed. **Magnus has not been approved for, nor applied to, any of these.** The page
includes a funding calculator (gap quantity × estimated cost per unit) and a short campaign
suggestion; for TBD quantities it prompts to verify the quantity before setting a target.

---

## Prototype Data

The current prototype uses **real public-source country need profiles** and **real
nonprofit support/resource programs**, entered as research-based data.

Country need profiles:

- Bangladesh — Cox's Bazar Rohingya response
- Ukraine — conflict-affected schools and children
- Sudan / Darfur — children affected by conflict and displacement
- Democratic Republic of the Congo — eastern DRC / cholera-affected areas
- Afghanistan — children and families needing basic services
- Syrian Arab Republic — displaced and returning children / host communities

**These are public-source need profiles, not confirmed Magnus partner requests. They
require verification before action.** Quantities and deadlines are TBD; source names
reference public humanitarian appeals (e.g. UNICEF country appeals) and each carries a note
to verify the receiving partner, exact quantities, and shipment acceptance before acting.

---

## Design System

Direction:

- A **green nonprofit / logistics color system** on a warm **off-white / beige** background.
- The **light blue from the Magnus logo** used as the main accent (hero accent word, primary
  call-to-action button, active sidebar icon, count badges).
- A **muted green card family** for dashboard preview cards, stepping deep → lighter green,
  with a single **muted blue** card.
- **Softer off-white text** inside colored cards instead of pure white.
- A clean, consistent **dashboard card system**.
- The **Magnus United logo** in the sidebar.
- A code-rendered **rotating Earth** visual in the hero.
- Layout structure inspired by polished modern product sites, but **original to Magnus Map**.

Key colors:

```css
--deep-green:         #0B241B;
--primary-green:      #12372A;
--lighter-green-card: #174A38;
--logo-blue:          #AFE1F6;
--muted-blue:         #4F9DB4;
--surface:            #FFF7E9;
--surface-soft:       #F7EED9;
--soft-white-text:    #EDE6D8;
--dark-text:          #0B241B;
```

Typography (active in the HTML version):

- **Bricolage Grotesque** — headlines, section topics, navigation, buttons, UI headings,
  metric numbers, labels, and table text.
- **BioRhyme** — body copy and supporting descriptions.

---

## Product Decisions

1. The product is **internal-first**, not public-facing.
2. The app uses **CSV storage** for simplicity.
3. The app **avoids external APIs** to keep the prototype stable.
4. Needs can be marked **TBD** instead of forcing fake numbers.
5. **Public-source humanitarian profiles are clearly separated** from verified Magnus
   requests (via Source Type and Verification Status).
6. Company programs are marked **Researching**, not Active or Approved.
7. The **Partner Finder is a manual research database**, not an AI-generated partner engine.
8. The **Action Plan avoids shipping actions** unless there is verified inventory and a
   verified receiving partner.

---

## What I Built

I built Magnus Map, an internal operations dashboard for Magnus United that organizes need
profiles, inventory, gaps, action items, partner research, and funding/resource
opportunities. The product is designed to help Magnus move from donation guesswork to
needs-based resource allocation — recording what is needed, comparing it against what is on
hand, ranking the gaps, and turning them into clear next steps, while keeping public-source
research strictly separated from anything confirmed.

---

## Current Limitations

- The app is a **local prototype**.
- Data is stored in **CSV files**.
- Country needs are **public-source profiles** that need verification.
- Company support programs are **research entries**, not confirmed partnerships.
- Exact quantities are often **TBD**.
- **No authentication** yet.
- **No automated partner discovery** yet.
- **No deployment** — the app runs locally only.
- **No external API integrations.**

---

## Future Improvements

- Add verified partner intake forms.
- Add source links for every need profile.
- Add proof-of-impact tracking after shipments.
- Add donor / sponsor outreach tracking.
- Add file uploads for receipts, shipment proof, and partner confirmation.
- Add user authentication.
- Add cloud database storage.
- Add exportable reports for sponsors and team meetings.
- Add a better partner verification workflow.
- Add dashboard filters for region, urgency, resource category, and verification status.

---

## Application Framing

Magnus Map shows that I identified a real operational problem inside Magnus United:
donations are easier to collect than to allocate intelligently. I built a tool that
connects public need signals, internal inventory, resource gaps, and possible support
programs into one decision system. The goal is to make Magnus more intentional about what it
sends, where it sends it, and what it needs to source next — without overstating what is
confirmed. Country needs are treated as public-source profiles requiring verification, and
company programs are tracked as research, not partnerships.
