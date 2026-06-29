---
name: portfolio-health-check
description: >-
  Reviews, rates, and optimizes a mutual-fund / SIP portfolio, then recommends the
  three highest-impact changes. Use this whenever the user shares a set of mutual funds
  with current invested values and/or monthly SIP amounts and wants it reviewed, rated,
  health-checked, rebalanced, or asks things like "how is my portfolio doing", "is this a
  good set of funds", "where should I put my new SIP money", or "what should I change".
  Works across every category (large / mid / small cap, flexi, ELSS, hybrid, debt, index,
  international). The skill browses Value Research Online itself via linked Chrome to pull
  each fund's performance, risk, holdings and valuation — so use it even when the user
  provides no links. Trigger generously: any list of named funds with rupee amounts
  attached is a strong signal, even if the user just pastes a table without asking a
  polished question.
---

# Portfolio Health Check

This skill turns a raw portfolio — a list of mutual funds with their invested values and
monthly SIPs — into an honest health-check and a short, ranked set of the changes that
would help most. The deliverable is a qualitative read of the portfolio's current state
(strengths, weaknesses, risk flags) plus the **three most impactful changes**, backed by
data you pull yourself from Value Research Online, and a live artifact the user can reopen.

## The mindset (read this first — it's what makes the analysis good)

The instructions below are a scaffold, not a script. The quality comes from holding a few
principles in mind throughout:

- **Rate funds within their own category, never across categories.** A small-cap fund
  returning 22% and a debt fund returning 7% are not "better" and "worse" — they do
  different jobs. Every comparison and judgement happens against category peers.

- **Corpus and inflows tell two different stories — always look at both.** The current
  invested value shows where the money *is*; the monthly SIP shows where it's *going*.
  A portfolio can look lopsided by corpus but be actively self-correcting through its
  SIPs, or vice versa. The gap between the two is usually where the interesting insight
  lives.

- **New SIP money is the cheapest rebalancing lever there is.** Redirecting future
  contributions triggers no exit load and no capital-gains tax, unlike selling existing
  units. So when you propose changes, reach for "redirect new money" first, and treat
  "sell / switch existing units" as a heavier move that the user must weigh for tax and
  exit-load reasons. Frame sell decisions as theirs to make, not yours to prescribe.

- **Multiple funds and multiple AMCs are often intentional — ask before calling them
  redundant.** People deliberately spread across fund houses to dilute AMC-level risk (a
  manager leaving, a governance issue), and they often run separate accounts for family
  members. What looks like over-diversification may be a feature. Confirm the setup before
  you critique it.

- **Index funds add cheap beta, not manager diversification.** When someone holds an index
  fund alongside several active funds in the same category, note that the index doesn't
  add a distinct manager bet — it's the benchmark the active funds are trying to beat.
  That's fine as a low-cost core, but it isn't "another fund" in the diversification sense.
  This also shades how you read AMC spread: holding the *same index* across three AMCs
  dilutes far less risk than spreading three active managers does, because index risk is
  mostly operational / tracking, not manager / strategy. Respect a deliberate spread, but
  it's fair to note the diminishing returns on duplicating an *index* in particular.

- **Valuation and concentration are forward-looking risk signals.** Past returns describe
  what happened; portfolio P/E, top-10 weight, and number of stocks hint at what could
  happen next. Pricey, concentrated funds tend to fall hardest in a correction. Use these
  to talk about future risk, not just past performance.

- **Check the user's factual claims against the data, not your priors.** When the user
  pushes back with a specific assertion ("the Next 50 has actually beaten the Nifty 50
  lately"), pull the numbers before you respond — generic framing ("Next 50 is just
  riskier") is often wrong on the specifics, and Value Research will settle it across
  1Y / 3Y / 5Y in seconds.

- **You are not a financial advisor.** Present analysis that helps the user decide; don't
  issue confident buy/sell commands. Caveat clearly, and leave tax-sensitive sell
  decisions to them.

## Step 0 — Understand the setup before analysing anything

Skim what the user gave you. Then, in a single concise round (use the AskUserQuestion tool
if available; otherwise ask in chat), clarify only what you genuinely need and don't
already know:

- **Goal and horizon** — retirement in 20 years, a house in 3, a child's education? This
  determines whether the category mix even makes sense (a 3-year goal sitting in small-cap
  funds is a problem regardless of how good those funds are).
- **Risk appetite** — how the user feels about a sharp drawdown.
- **Is the multi-fund / multi-AMC spread intentional?** e.g. separate accounts for spouse
  / parents, or deliberate AMC-risk dilution. This decides whether overlap is a flaw or a
  feature.
- **What sits *outside* this MF portfolio?** Ask for the rest of the household balance
  sheet — FD, EPF/PF, NPS, cash/savings, real estate, direct equity. A book that looks 2%
  debt internally can be ~35% debt once a big FD + PF balance is counted, which completely
  changes the risk read. **Do not flag "too aggressive", "no ballast", or "no emergency
  fund" until you know what's held outside the funds.**
- **Are any of these funds closed or frozen to fresh investment?** International / overseas
  fund-of-funds in particular get shut to inflows under the RBI/SEBI overseas cap. This
  cuts both ways: you can't recommend topping them up, and you must **not** recommend
  exiting them either, since re-entry may be blocked.
- **Confirm the plan on every holding — are they all Direct?** Ask explicitly and have the
  user flag any Regular ones; don't wait for a name to look ambiguous. Users routinely
  mislabel a Regular plan as Direct (or vice versa), and pulling the wrong plan invents a
  bogus expense-ratio / "switch to Direct" recommendation off numbers that aren't theirs.
- **Resolve any ambiguous or missing fund identity.** If a name doesn't map cleanly to one
  scheme on Value Research, ask the user to confirm the exact fund — including plan
  (Direct vs Regular) and option (Growth vs IDCW), since these change the numbers. Never
  silently guess which fund they meant.

Don't over-interview. If the user already stated their goal and setup, skip straight to the
work. Bundle questions so the user answers once.

## Step 1 — Organize the data

Build a working table before touching the browser:

| Fund | Category | Current value | Monthly SIP |

Then compute and keep handy:

- **Corpus weight %** (each fund's share of total invested value) and **category corpus
  weight %**.
- **Inflow weight %** (each fund's share of total monthly SIP) and **category inflow
  weight %**.
- Group the funds by category.

Immediately note any place where corpus weight and inflow weight diverge sharply — that
contrast often drives the most useful observation later.

## Step 2 — Pull the data from Value Research yourself (linked Chrome)

Do this on your own using the Claude-in-Chrome tools. Do not wait for the user to provide
links; you are expected to go get the data.

**Method — work category by category:**

**At scale (say 40+ funds), triage rather than deep-reading everything.** Lean on the
headline block plus trailing returns for most funds, and reserve the full risk-ratio /
holdings work-up for the funds receiving the largest SIPs or flagged as outliers. Pure
index funds only need expense ratio and tracking — not a full work-up.

1. Open Value Research's Fund Compare tool:
   `https://www.valueresearchonline.com/funds/fund-compare/`. **Add each fund by typing its
   name into the search box and picking from the autocomplete**, then verify that every
   fund you intended actually appears in the comparison before reading anything. The picker
   has a quirk: clicking a suggestion does **not** create a chip — it leaves the name as
   text in the box and unlocks the *next* box. So the reliable pattern is click box → type
   → wait for the dropdown → click the top suggestion → let the next box activate. Don't
   click the next box while the dropdown is still open, or the click lands back in the
   current box and appends text; confirm with a screenshot after each pick. Prefer
   this over constructing the compare URL from `peer-fund-search` parameters: the URL
   method *silently drops* any fund whose name string doesn't exactly match Value
   Research's official scheme name, and you won't notice the fund is missing. Users often
   write funds differently from how VR lists them (e.g. "HDFC Mid-Cap Opportunities" is
   "HDFC Mid Cap" on VR; "Kotak Emerging Equity" now shows as "Kotak Midcap"), so always
   confirm the matched scheme is the one the user meant. **If the search returns no
   confident match — or several plausible ones — just ask the user** which exact scheme
   they hold (fund house + scheme name, and plan/option if unclear) rather than guessing or
   silently analysing the wrong fund. A quick question is far cheaper than a confident
   answer about the wrong fund. The tool compares up to **5 funds at a time** — if a
   category has more, run it in batches.

2. For each comparison, read every tab — they are anchor links across the top:
   - **Headline row:** Net Assets (AUM), Return Since Launch, Riskometer, Exit Load,
     Expense Ratio, Fund Age, Portfolio Turnover.
   - **Trailing Returns** (YTD, 1M, 3M, 6M, 1Y, 2Y, 3Y, 5Y, 7Y, 10Y).
   - **Risk Ratios** (Mean, Std Dev, Sharpe, Sortino, Beta, Alpha).
   - **Asset Allocation** and **Sector Distribution**.
   - **Holdings** (Portfolio P/E, P/B, No. of Stocks, Top 3 Sectors %, Top 5 / Top 10
     stock concentration, and the Top Holdings list).

   Note: `get_page_text` often returns only the first (Trailing Returns) tab because the
   others load as hidden panels. After clicking a tab, read it with a **screenshot** or
   `read_page` on the now-visible table rather than trusting `get_page_text`. That first
   read still hands you the whole headline block (AUM, expense ratio, exit load, turnover,
   riskometer, return-since-launch) plus trailing returns — which is enough to judge most
   funds on its own. Only spend clicks on the Risk Ratios / Holdings tabs when they'd
   actually change the verdict (a large-SIP fund, a flagged outlier, a valuation question).

3. **If only one of the user's funds sits in a category,** open that fund's own Value
   Research page instead, and capture the same metrics plus its **category rank /
   percentile** so you can judge it against the category average.

4. **Estimate overlap** from the Top Holdings tables: scan how often the same stocks repeat
   across funds in a category and at what weights. Heavy repetition = genuine overlap; many
   distinct names = the funds are actually doing different things.

5. **If a fund can't be found,** stop and ask the user for the exact scheme name rather
   than analysing the wrong fund.

Capture per fund: trailing returns across periods, the risk ratios, expense ratio, exit
load, turnover, concentration (no. of stocks, top-10 %), valuation (P/E, P/B), sector
tilts, and the top holdings.

## Step 3 — Supplement with a quick web search

Value Research is a snapshot. For any fund that stands out — a big recent under- or
over-performance, an unusually concentrated book, a sharp AUM change — run a quick web
search for recent context: fund-manager changes, strategy shifts, why it's lagging or
leading, and the category's near-term outlook. This catches things the numbers hide, such
as a star manager having just left or a fund being mid-transition.

## Step 4 — Evaluate through these lenses

Work through each lens; for each, the point is *what good vs bad looks like and why*:

1. **Allocation shape** — corpus vs inflows vs goal. Is the money concentrated where the
   conviction (and quality) is? Are the SIPs already correcting a lopsided corpus, or
   reinforcing the imbalance?
2. **Category mix vs goal & horizon** — is there too much small/mid risk for a near-term
   goal, or no large-cap / debt ballast for someone close to needing the money? Is there
   needless category sprawl? Judge ballast and emergency cover at the **household** level
   using the outside-the-MF assets from Step 0 — the funds may look equity-heavy in
   isolation while the household is well-cushioned by FD / PF / cash.
3. **Fund quality within category** — judge on returns *and* risk-adjusted metrics
   (Sharpe, Sortino, alpha) *and* consistency across periods, not trailing return alone. A
   fund can have a great 5-year number while being structurally impaired right now
   (manager exit, broken strategy).
4. **Intra-category overlap / redundancy** — only call funds redundant within a single
   account/holder; respect intentional AMC spread and family-account splits established in
   Step 0.
5. **Valuation & forward risk** — P/E, P/B, concentration, top-10 weight. Name which funds
   carry the most downside in a correction and why.
6. **Cost & flexibility** — expense ratio, Direct vs Regular plan (Regular is pure drag if
   there's no advisor adding value), exit loads that limit manoeuvrability.
7. **AMC / manager concentration** — respecting the user's stated intent, still flag
   genuine single points of failure (everything under one AMC, or a fund mid
   manager-transition).
8. **Access constraints (closed / frozen funds)** — for any holding flagged in Step 0 as
   closed to fresh investment, treat it as effectively one-way: **never propose
   consolidating or exiting it** (re-entry may be blocked under the inflow cap) unless the
   user explicitly wants out entirely, and never propose topping it up. Work around it
   rather than through it.

## Step 5 — Deliver: the health-check + the 3 most impactful changes

Write the chat response in this shape:

1. **Comparison tables**, one per category, with the key metrics side by side.
2. **Health-check** — qualitative, no score or grade. Three honest, specific paragraphs or
   groupings: **Strengths**, **Weaknesses**, **Risk flags**. Be concrete and candid; avoid
   generic praise.
3. **The 3 most impactful changes**, ranked by impact. For each one give:
   - *What* to change (name the actual funds and rough rupee/percent moves).
   - *Why* it matters (tie it back to a lens above).
   - *How* to action it with least friction — prefer redirecting **new SIP money**; where a
     sell/switch is genuinely needed, say so and flag it as a tax / exit-load decision the
     user should make.
   Resist listing more than three — the discipline of picking the top three is the value.

   **Conserve the rupees.** Whenever a change reshuffles SIPs, the proposed target SIP total
   must equal the current SIP total plus any amounts you've freed up — don't quietly invent
   or lose money. Show the reconciliation so the plan is actually executable, not just
   illustrative.
4. **Close** with the not-a-financial-advisor caveat and an offer to go deeper — in
   particular, offer a concrete **current-vs-target SIP table** (per-fund current vs target,
   plus an explicit "stop these SIPs, keep the units" list and any hold-or-switch calls on
   the existing corpus with exit-load / tax flags). Users almost always want this after the
   health-check.

Keep the prose natural and readable — tables where they earn their place, plain paragraphs
elsewhere, not a wall of bullets.

## Step 6 — Build the live artifact

Use the `create_artifact` tool to save a self-contained summary page the user can reopen
later. The portfolio data does not come from a connector, so **embed the analysis directly
in the page** (no connector calls needed). Include:

- The per-category comparison tables.
- Allocation visuals — corpus-vs-inflow and category-mix charts. Chart.js via CDN is
  allowed; keep it clean.
- The health-check summary (strengths / weaknesses / risk flags).
- The three changes, clearly ranked.

Tell the user the artifact is saved and reopenable, then offer next steps.

## A note on tone and honesty

The user is trusting you to tell them what's actually going on, including the unflattering
parts (a beloved fund that's now the weak link, a costly Regular-plan holding, a goal
mismatch). Be direct and specific, explain your reasoning so they can disagree, and keep
every recommendation grounded in the data you pulled — not in generic "diversify more"
boilerplate.
