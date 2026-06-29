# portfolio-health-check

An agent skill that reviews, rates, and optimizes a mutual-fund / SIP portfolio, then
recommends the three highest-impact changes.

Given a list of funds with current invested values and monthly SIP amounts, the skill:

- Clarifies the goal, horizon, and setup (and respects intentional AMC / family-account spread).
- Browses Value Research Online directly (via linked Chrome) to pull each fund's
  performance, risk ratios, holdings, concentration, and valuation — category by category.
- Supplements with a quick web search for recent context (manager changes, outlook).
- Delivers a qualitative health-check (strengths / weaknesses / risk flags) and the
  **3 most impactful changes**, prioritizing low-friction redirection of new SIP money.
- Builds a reopenable live artifact summarizing the analysis.

It rates funds only within their own category, separates corpus weights (by invested value)
from inflow weights (by monthly SIP), and treats index funds as cheap beta rather than
manager diversification. It is an analysis aid, not financial advice.

## Files

- `SKILL.md` — the skill definition and instructions.
- `evals/evals.json` — test portfolios used to validate the skill.

## Install

Place this folder in your agent's skills directory, or load it however your AI agent or
assistant supports skills.
