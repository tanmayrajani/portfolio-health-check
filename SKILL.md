---
name: portfolio-health-check
description: >-
  Reviews, rates, and optimizes a mutual-fund / SIP portfolio, then recommends the
  three highest-impact changes. Use this whenever the user shares a set of mutual funds
  with current invested values and/or monthly SIP amounts and wants it reviewed, rated,
  health-checked, rebalanced, or asks things like "how is my portfolio doing", "is this a
  good set of funds", "where should I put my new SIP money", or "what should I change".
  Works across every category (large / mid / small cap, flexi, ELSS, hybrid, debt, index,
  international). The skill browses Value Research Online itself to pull each fund's
  performance, risk, holdings and valuation — so use it even when the user provides no
  links. Trigger generously: any list of named funds with rupee amounts attached is a
  strong signal, even if the user just pastes a table without asking a polished question.
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

- **Valuation and concentration are forward-looking risk signals.** Past returns describe
  what happened; portfolio P/E, top-10 weight, and number of stocks hint at what could
  happen next. Pricey, concentrated funds tend to fall hardest in a correction. Use these
  to talk about future risk, not just past performance.

- **You are not a financial advisor.** Present analysis that helps the user decide; don't
  issue confident buy/sell commands. Caveat clearly, and leave tax-sensitive sell
  decisions to them.

## Step 0 — Understand the setup before analysing anything

Skim what the user gave you. Then, in a single concise round (use a structured
clarifying-question tool if one is available; otherwise just ask in chat), clarify only
what you genuinely need and don't already know:

- **Goal and horizon** — retirement in 20 years, a house in 3, a child's education? This
  determines whether the category mix even makes sense (a 3-year goal sitting in small-cap
  funds is a problem regardless of how good those funds are).
- **Risk appetite** — how the user feels about a sharp drawdown.
- **Is the multi-fund / multi-AMC spread intentional?** e.g. separate accounts for spouse
  / parents, or deliberate AMC-risk dilution. This decides whether overlap is a flaw or a
  feature.
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

## Step 2 — Pull the data from Value Research yourself

Do this on your own using whatever web-browsing tools you have available (a browser tool,
fetch tool, or web search). Do not wait for the user to provide links; you are expected to
go get the data.

**Method — work category by category:**

1. Open Value Research's Fund Compare tool:
   `https://www.valueresearchonline.com/funds/fund-compare/`. **Add each fund by typing its
   name into the search box and picking from the autocomplete**, then verify that every
   fund you intended actually appears in the comparison before reading anything. Prefer
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

   Note: a plain text-extraction of the page often only returns the first (Trailing
   Returns) tab because the others load as hidden panels. After switching tabs, re-fetch
   or re-render the page (a screenshot, or re-reading the now-visible DOM/HTML) rather than
   trusting the first text dump.

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
   needless category sprawl?
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
4. **Close** with the not-a-financial-advisor caveat and an offer to model a specific split
   or go deeper.

Keep the prose natural and readable — tables where they earn their place, plain paragraphs
elsewhere, not a wall of bullets.

## Step 6 — Build a reopenable summary

If your environment supports a live/reopenable artifact (e.g. a canvas, artifact panel, or
HTML preview), use it to save a self-contained summary page; otherwise produce a clean
Markdown or HTML file the user can save and reopen. Embed the analysis directly — the
portfolio data isn't pulled from a connector, so no external calls are needed at this
stage. Include:

- The per-category comparison tables.
- Allocation visuals — corpus-vs-inflow and category-mix charts, if your output format
  supports charts (e.g. Chart.js via CDN in an HTML artifact); otherwise a clear table or
  ASCII/text breakdown is fine.
- The health-check summary (strengths / weaknesses / risk flags).
- The three changes, clearly ranked.

Tell the user the summary is saved and reopenable (or where to find the file), then offer
next steps.

## A note on tone and honesty

The user is trusting you to tell them what's actually going on, including the unflattering
parts (a beloved fund that's now the weak link, a costly Regular-plan holding, a goal
mismatch). Be direct and specific, explain your reasoning so they can disagree, and keep
every recommendation grounded in the data you pulled — not in generic "diversify more"
boilerplate.
