# Household Net Worth

A private tracker and forecaster for a household's finances: what you own and owe today, your income and spending, and where your net worth is headed, including what buying a home or having kids would do to it.

It has seven tabs:

| Tab | What it's for |
|---|---|
| **Net worth** | Every account by owner and type, assets, debts, and equity |
| **Income** | Salary, bonus, stock grants, refreshers, rental income, family gifts (only the types you use) |
| **Expenses** | Monthly spending by category, including current mortgage payments |
| **Net worth (forecast)** | Year-by-year projection with taxes, savings, investment growth, and a chart |
| **Mortgage (forecast)** | Simulate a home purchase, including how the down payment is funded (gift, family loan, HELOC) |
| **Kids (forecast)** | Monthly costs per child by age, inflation by category, and 529 savings |
| **Setup** | Names, filing status, state taxes, and which income types appear |

Taxes cover federal income tax, Social Security, Medicare, and your state's income and payroll taxes. California is built in; Claude sets up any other state for you.

This is a planning tool, not financial advice.

## Option 1: With Claude (recommended)

Claude builds your own private copy, asks a few questions so it fits how your household's money works, and can keep your balances up to date.

1. Download this repo as a zip (green **Code** button → **Download ZIP**) and unzip it.
2. Add the `skill/household-net-worth` folder to Claude as a skill from Claude's settings (zip that folder on its own if Claude asks for a .zip).
3. Start a new chat and say: **"Build my household net worth tracker."**

Claude will ask three things first: who's in the household, where you live, and how you file taxes. Everything else (income, balances, spending, plans) is optional. Skip any of it and fill it in on the page later.

Your copy is published as a private Claude artifact in your own account. Only you can see it until you share it from its **Share** menu, for example with your partner. To update it later, open a chat and say **"refresh my net worth tracker"**, and send new screenshots or log in when asked.

## Option 2: In a browser, no Claude needed

Open `skill/household-net-worth/assets/tracker.html` in any browser.

- Use the **Setup** tab to enter names, filing status, state taxes, and income types.
- Add accounts at the bottom of the **Net worth** tab and edit balances with the pencil buttons.
- Your data is saved **in that browser only**. Use **Download backup** on the Setup tab regularly, and **Restore from backup** to move to another browser or device.
- State taxes in this mode are California or a state with no income tax. Other states need Claude to set up their brackets.

## Privacy

- **No financial data belongs in this repo.** The page stores your numbers in your own Claude artifact or your own browser, never in the HTML file and never on GitHub. Don't commit backups or screenshots here.
- **Passwords are never stored.** When Claude reads balances from a website, you log in yourself and Claude logs out afterward.
- Account numbers are kept to the last 4 digits at most.

## What's in the repo

```
skill/household-net-worth/
  SKILL.md            Instructions Claude follows: the interview, building your copy, refreshing
  assets/tracker.html The tracker page (works in Claude and in any browser)
```

## Model notes

- Federal brackets and standard deductions are for 2026; state figures are the latest each state has published. Brackets are held at today's levels in the forecast.
- Stock grants vest quarterly (Jan 1, Apr 1, Jul 1, Oct 1). Vesting counts as wages in the year it vests.
- Rental income is taxed in full (expenses and depreciation aren't modeled). Pre-tax 401(k) contributions aren't modeled, so taxes run slightly high.
- Investment returns, home value change, salary growth, expense growth, and inflation by kids' category are all inputs you set. Nothing is assumed for you.
