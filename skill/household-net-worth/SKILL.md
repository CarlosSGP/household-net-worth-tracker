---
name: household-net-worth
description: Build and refresh a private household net worth tracker and forecaster (net worth, income, expenses, net worth forecast, home purchase and kids planning), with taxes set for the state the household lives in. Use when someone asks to set up, build, or refresh their net worth tracker or financial forecast.
---

# Household net worth tracker

This skill builds a private, interactive tracker for one household and keeps it up to date. The finished page is in `assets/tracker.html`. Your job is to learn the basics of how this household's money works, publish their own copy of that page, fill in whatever they're ready to share, and refresh it when they ask.

The page has seven tabs: **Net worth** (accounts by owner and category), **Income**, **Expenses**, **Net worth (forecast)**, **Mortgage (forecast)**, **Kids (forecast)**, and **Setup**. Income and Expenses are inputs only; all projections live on the forecast tabs. Every number can also be entered or edited on the page itself, so nothing has to be collected up front.

## Ground rules

- **Publish the template exactly as it is.** Don't redesign, restyle, or rewrite any part of `assets/tracker.html`; the only change is the `<title>`. If something on the page looks wrong or missing, tell the person instead of editing the page.
- **Passwords are never asked for, typed, or stored.** If balances come from a website, the person logs in themselves in the browser, and you log out when you're done. Never save a login. Store at most the last 4 digits of any account number.
- **Don't invent numbers.** Every amount comes from the person, a screenshot, or a site they're logged into. Leave anything unknown blank; the page shows an input for empty fields.
- **Each household gets its own copy.** Never write one household's data into another household's tracker.
- This is a planning tool, not financial advice. Say so once if they ask for a recommendation.

## Step 1: The basics (always ask)

Ask these three before building. They shape the whole model.

1. **Who's in the household?** Just them, or them and a partner, and the names to use.
2. **Where do they live?** City and state.
3. **Tax filing status:** married filing jointly, or single.

### Setting up taxes for where they live

The page models federal tax, Social Security and Medicare for everyone. State taxes come from `meta/config.state`:

- **California:** built in. You can leave `state` out of the config.
- **Any other state:** look up the state's current figures from official sources (the state's tax agency first) and write them into `state` (shape below):
  - Income tax brackets and standard deduction for their filing status. Use the latest tax year the state has published, and tell the person which year you used.
  - Any surcharge on high incomes (for example, an extra rate above $1M).
  - Employee payroll taxes on wages, such as disability or paid family leave programs (New York, New Jersey, Washington, Massachusetts, Rhode Island, Hawaii, Oregon, and others have them), with the rate and any wage cap.
  - States with no wage income tax get `"income": null`.
- **Local income taxes:** if their city or county has one (New York City, many Ohio and Pennsylvania cities, some Maryland counties, and others), add it to the state brackets as a combined rate and say you did.
- Tell them in one or two sentences what you set up and where the numbers came from. Mention that the property tax rate on the Mortgage (forecast) tab should be their county's rate.

## Step 2: Everything else is optional

For each topic below, first ask if they're ready to go through it now. If not, skip it and tell them they can fill it in later directly on the page (or ask you any time). Keep it conversational: one topic at a time, multiple choice where it fits (use the question tool if you have one), and skip anything they've already told you. Never hold up the build waiting for these.

**Income** (ask once per person)
- Base salary.
- An annual bonus? If yes, roughly what percent of base.
- Stock grants (RSUs)? If yes, which company and which stock plan site their employer uses. You can pull the grants once the tracker exists.
- A new stock grant (refresher) each year? If yes, its value at grant and how many years it vests over.
- Rental income? If yes, the monthly amount.
- Recurring cash gifts from family? If yes, roughly how much per year.

Turn on only the income types they say yes to (`meta/config.features`); the Income tab shows just those. If they skip this topic, leave the defaults; they can turn income types on in the Setup tab later.

**Account balances**
- Offer two ways: **log in securely** to their bank, brokerage, or a budgeting app that shows all their accounts in the browser, where they type their own password and nothing is stored, or **send screenshots** of each account. Either way, they can also add or correct accounts on the page.
- Which accounts are joint? Does anyone co-own a home or other property with someone outside the household? If so, what share is theirs? (Only that share counts toward net worth; the full mortgage still counts if it's in their name.)

**Monthly spending**
- Housing (rent or mortgage payment), groceries, travel, medical, entertainment, dining out, incidentals, and car lease. Offer to pull recent spending if they have a connected account.

**Plans**
- Are they thinking about buying a home or having kids? Those simulators start empty and switched off; they can fill them in on the page whenever they like.

## Step 3: Build their copy

1. Read `assets/tracker.html` in full.
2. Change only its `<title>` to a short name for the household, e.g. `Ana &amp; Luis Net Worth` or `Maria's Net Worth`. Write the result to your scratch directory as `tracker.html`.
3. Publish it as an artifact with `capabilities: {"db": {}, "user": {}}` and icon `wallet`. The page reads all of its data from the artifact's own database, so no numbers go into the HTML.
4. Seed the database with one batch write (see the data model below):
   - `meta/config` from the basics: names, filing status, state taxes, and income types if they answered the income questions.
   - Whatever they shared: `income/p1` (and `income/p2`), `accounts`, `grants`, `expenses`, `mortgages`, `meta/assumptions`.
   - `meta/status` with `lastRefresh` set to today, if you added balances.
5. Read the collections you wrote back once to confirm they landed.
6. Tell them, in a few sentences, what's on the page and what's still blank (and that they can fill it in on the page or with you), and that the page is private until they share it from its Share menu (for example, with their partner).

### Getting balances

- **Screenshots:** read each one, extract every individual account (not page totals), and check that the accounts on each page add up to that page's total. Mention anything that doesn't reconcile.
- **Logging in:** open the site in the browser and ask them to log in themselves; their password is never seen or saved. Read the balances, then log out and confirm the site shows its login page again.
- **Stock plans:** from the plan's holdings page, record each unvested grant: unvested shares, shares per quarterly vest, next vest date, and today's share price. Shares already vested and held belong in `accounts` as an investment, not in `grants`. Check that vested value + unvested value + any unreleased dividends equals the plan's total account value, and only raise it if it doesn't match.

## Step 4: Refresh

When they say "refresh" or send new screenshots: get the new balances the same way, update the matching `accounts` documents (balance and `asOf`) rather than creating duplicates, update grants if they pulled the stock plan, set `meta/status.lastRefresh` to today, and briefly tell them what changed. Log out of every site at the end of each refresh. If they move to another state, update `meta/config.state` the same way as in Step 1.

## Data model

All documents live in the artifact's database. Owners are always keys: `p1`, `p2`, or `joint`.

- **`meta/config`**:
  ```json
  {
    "people": [{"name": "Ana"}, {"name": "Luis"}],
    "filing": "mfj",
    "features": {"p1": {"bonus": true, "stock": true, "refresher": true, "rental": false, "gifts": false}, "p2": {"bonus": true}},
    "state": {
      "code": "NY", "name": "New York",
      "income": {"mfj": {"brackets": [[0, 0.04], [17150, 0.045]], "std": 16050}, "single": {"brackets": [[0, 0.04]], "std": 8000}},
      "surcharge": null,
      "payroll": {"label": "NY SDI + Paid Family Leave", "rate": 0.0043, "wageCap": null}
    }
  }
  ```
  - `filing` is `"mfj"` or `"single"`. The first person is `p1`, the second `p2`; omit the second person for a one-person household.
  - Base salary always shows; `features` turns on the other income fields (bonus is on by default, the rest off).
  - `state.income` brackets are `[taxable income where the rate starts, rate]`, applied after subtracting `std`. Use `null` for no income tax. `surcharge` is `{"threshold", "rate"}` or `null`. `payroll` is an employee tax on wages per person, or `null`. Omit `state` entirely for California.
- **`accounts/<id>`**: `{ "name", "institution", "last4" (optional), "owner": "p1"|"p2"|"joint", "kind": "asset"|"debt", "category", "balance", "asOf": "YYYY-MM-DD", "source" }`.
  - Asset categories: `cash`, `investments` (taxable brokerage, vested stock), `retirement` (401(k), IRA, HSA), `property` (home, car).
  - Debt categories: `mortgage`, `credit_card`, `loan`.
  - Co-owned property: add `"ownershipShare": 0.5` (their share) and `"coOwners": "…"`. Name homes with "Home" so the forecast and HELOC tools find them.
  - A mortgage can point to its home with `"securedBy": "<home account id>"` to show equity on the Net worth tab.
- **`income/p1`, `income/p2`**: `{ "base", "bonusPct", "rental" (per month), "gifts" (per year), "refresher" (grant value per year), "refresherYears" }`.
- **`grants/<id>`**: `{ "owner", "name", "ticker", "grantDate", "unvestedShares", "sharesPerQuarter", "nextVest": "YYYY-MM-DD", "price", "source", "asOf" }`. Vests are quarterly (Jan 1, Apr 1, Jul 1, Oct 1). A grant whose next vest is Dec 31 keeps Dec 31 as its January vest date.
- **`meta/assumptions`**: `{ "projectedPrices": {"TICKER": price}, "unreleasedDividends" (optional) }`. Set each ticker's projected price to today's price unless they give one.
- **`expenses/<category>`**: `{ "monthly" }` for `rent`, `groceries`, `travel`, `medical`, `entertainment`, `dining`, `incidentals`, `carLease`.
- **`mortgages/<id>`**: `{ "name", "monthly", "owner", "order" }`, for current mortgage payments.
- **`meta/spending`** (optional): `{ "source": "…", "categories": { "<category>": {"monthly", "note"} } }` shows recent spending as a hint under each expense.
- **`meta/forecast`**, **`sim/mortgage`**, and **`kids/<id>`** hold the forecast assumptions and simulators. The person fills these in on the page; only seed them if they give you numbers.

## Using it without Claude

`assets/tracker.html` also works on its own: open it in any browser. It saves data in that browser only. The Setup tab covers names, filing status, income types, and state taxes (California or a state with no income tax; other states need Claude to set their brackets). Accounts can be added on the Net worth tab, and a backup/restore button on the Setup tab moves data between devices.
