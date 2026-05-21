# Claude for Financial Services — Plugins & Agents

Reference agents, skills, and data connectors for the financial-services workflows we see most — investment banking, equity research, private equity, wealth management, and fund administration. Install the ones that match your work, then tune them to how your firm operates.

> [!IMPORTANT]
> Nothing in this repository constitutes investment, legal, tax, or accounting advice. These agents draft analyst work product — models, memos, research notes, reconciliations — for review by a qualified professional. They do not make investment recommendations, execute transactions, bind risk, post to a ledger, or approve onboarding; every output is staged for human sign-off. You are responsible for verifying outputs and for compliance with the laws and regulations that apply to your firm. See [Important Risks Using These Plugins](#important-risks-using-these-plugins).

## What's in here

These plugins come in four families:

- **Vertical plugins** — skills, slash commands, and data connectors bundled by business area (`financial-analysis`, `investment-banking`, …). Install these for `/comps`, `/dcf`, `/earnings`, and the connectors.
- **Named agents** — autonomous, multi-step workers you hand a goal (Pitch Agent, GL Reconciler, …). Each is self-contained and bundles the skills it uses.
- **Partner plugins** — analytics powered by LSEG and S&P Global data.
- **Admin tooling** — `claude-for-msft-365-install` provisions the Claude Microsoft 365 add-in against your own cloud (IT admins only).

## Installation (manual upload)

These steps cover uploading the plugins by hand — no GitHub connector required. Prebuilt, ready-to-upload zips live in [`claude-upload-packages/individual-plugins/`](./claude-upload-packages/individual-plugins) — one per plugin.

> [!NOTE]
> The upload dialog validates **exactly one `plugin.json` per zip**, so you upload each plugin individually and group them under one marketplace as you go. A single archive containing all plugins is rejected.

**Upload the first plugin — create the marketplace**

1. In claude.ai, go to **Admin settings → Plugins → Add plugins → Upload plugin**.
2. Choose **Upload to a new marketplace** and give it a name (e.g. `claude-for-financial-services`).
3. Drag in `financial-analysis.zip` — install this first, since the other verticals build on its modeling skills — and click **Upload**.

**Add the remaining plugins to that marketplace**

4. Open the dialog again, choose **Add to an existing marketplace**, and select the marketplace you just created.
5. Drag in the next zip and click **Upload**. Repeat for each plugin you want.

When you're done, every uploaded plugin appears in that marketplace as **Available to install**; install the ones you want from the list. (`claude-for-msft-365-install` is admin tooling, not an end-user plugin — see below.)

### Building a zip from source

The prebuilt zips are convenient, but you'll want to repackage a plugin after you customize it. Each plugin directory carries its own `.claude-plugin/plugin.json`; zip the directory and upload it:

```bash
cd plugins/agent-plugins
zip -r pitch-agent.zip pitch-agent -x '*.DS_Store'
```

### Other ways to install

- **Cowork (from this repo):** Settings → Plugins → Add plugin → paste the repo URL, then pick plugins from the marketplace list.
- **Claude Code:** `claude plugin marketplace add <your-org>/<repo>`, then e.g. `claude plugin install financial-analysis@claude-for-financial-services`.
- **Claude Managed Agents:** each named agent also ships as a [Managed Agent template](./managed-agent-cookbooks); deploy with `scripts/deploy-managed-agent.sh <slug>`.

## How to use these plugins

- **Slash commands** — type `/` in a session to see available commands, then pick one (e.g. `/financial-analysis:dcf`). Commands give you a guided starting point for a specific deliverable.
- **Just describe the task** — many capabilities are *skills* that fire automatically when your request matches; no command needed. Plainly describe what you need and attach any files.
- **Named agents** — agents are dispatched with a goal and then run the whole multi-step workflow on their own (pulling data, building models, drafting documents). Address the agent by name and state the outcome you want.
- **Data connectors** — some plugins pull live market and company data through connectors (FactSet, S&P Global, LSEG, PitchBook, Morningstar, and others). These require your firm's own data subscriptions, configured after install.

## All plugins at a glance

| Plugin | Family | What it's for |
|---|---|---|
| `financial-analysis` | Vertical | Core modeling: DCF, comps, LBO, 3-statement, deck QC |
| `investment-banking` | Vertical | Teasers, CIMs, buyer lists, merger models, deal tracker |
| `equity-research` | Vertical | Earnings, initiations, morning notes, screens, theses |
| `private-equity` | Vertical | Deal screening, DD, IC memos, returns, value creation |
| `wealth-management` | Vertical | Client reviews, reports, plans, rebalancing, tax-loss |
| `fund-admin` | Vertical | GL recon, break tracing, accruals, roll-forwards, NAV tie-out |
| `operations` | Vertical | KYC document parsing and rules-grid evaluation |
| `pitch-agent` | Named agent | Comps + precedents + DCF to a branded pitch deck |
| `market-researcher` | Named agent | Sector/theme to overview, comps, and an ideas shortlist |
| `earnings-reviewer` | Named agent | Earnings event to model update to note draft |
| `meeting-prep-agent` | Named agent | Client-meeting briefing pack from CRM and holdings |
| `model-builder` | Named agent | DCF, LBO, 3-statement, comps live in Excel |
| `gl-reconciler` | Named agent | Finds breaks, traces root cause, routes for sign-off |
| `kyc-screener` | Named agent | Parses onboarding docs, runs rules, flags gaps |
| `valuation-reviewer` | Named agent | GP packages to valuation template to LP reporting |
| `month-end-closer` | Named agent | Accruals, roll-forwards, variance commentary |
| `statement-auditor` | Named agent | Audits LP statements vs NAV pack before distribution |
| `lseg` | Partner | FI/rates/FX/options analytics on LSEG data |
| `sp-global` | Partner | Tear sheets, earnings previews, funding digest (S&P) |
| `claude-for-msft-365-install` | Admin | Provision the Claude MS-365 add-in on your own cloud |

## Important Risks Using These Plugins

AI-generated analysis is a starting point, not a finished work product. Every model, note, screen, and report these plugins produce can contain errors and must be reviewed by a qualified person before it is relied on or shared. The points below apply to all of the plugins in this guide.

- **Data accuracy and AI errors** — Outputs can be wrong. The underlying models can miscalculate, misread a filing, omit relevant facts, rely on outdated figures, or state confidently incorrect details ("hallucinations"). Clean formatting is not evidence of accuracy.
- **Review every cited source** — When an output cites a filing, transcript, release, or data point, open it and confirm the figure actually appears there, means what the output says it means, and is the most recent available. Treat anything uncited as unverified.
- **Corroborate with independent sources** — Cross-check key numbers and claims against primary documents and at least one independent source before acting on them. Do not let one tool's output stand as the validation for another's.
- **Never the sole basis for material decisions** — Materially important investment, valuation, credit, trading, or allocation decisions must never be made solely on AI-generated content or research. Use these outputs to accelerate your own analysis — not to replace independent judgment, required approvals, and sign-off.
- **Model risk** — Financial models (DCF, LBO, comps, merger, returns) are highly sensitive to inputs and assumptions, and small changes can swing the conclusion. Audit formulas and assumptions — for example with `/financial-analysis:debug-model` or the `audit-xls` skill — before circulating, and document the assumptions you relied on.
- **Data-connector and entitlement limits** — Connector results (FactSet, S&P Global, LSEG, Morningstar, PitchBook, and others) are only as good as the underlying feed and your firm's subscription coverage. Expect gaps, reporting lags, and point-in-time snapshots that go stale as markets move, and respect each provider's licensing terms.
- **Regulatory and compliance workflows** — For KYC/AML, sanctions and PEP screening, reconciliations, month-end close, and LP statements, AI output does not satisfy your regulatory or control obligations. These still require the independent review, four-eyes checks, and authorized sign-off your firm and regulators mandate; a missed match or undetected break can carry legal and financial consequences.
- **Confidential and non-public information** — These tools handle deal materials, client data, and other sensitive content. Follow your firm's information-barrier, data-handling, privacy, and records policies, and take particular care with material non-public information.
- **Not legal, tax, or investment advice** — Financial plans, tax-loss harvesting, proposals, and similar outputs are not professional advice. Validate them with qualified legal, tax, and compliance professionals and apply the applicable suitability and fiduciary standards.
- **Templates to tune, not turnkey** — The agents and skills are starting points that reflect general workflows. Adapt them to your firm's methodology, data, controls, and house style, and confirm they behave as intended before depending on them.

## Plugin reference

### Vertical plugins

Skills and slash commands grouped by business area. Install `financial-analysis` first — the others build on its modeling skills.

#### financial-analysis

**What it does:** The core modeling toolkit: DCF, trading comps, LBO, 3-statement models, competitive-landscape analysis, plus spreadsheet auditing and deck quality checks. Pulls market data from connectors including FactSet, S&P Global, Morningstar, PitchBook, LSEG, Moody's, and Daloopa.

**How to use:** Commands: `/financial-analysis:dcf`, `:comps`, `:lbo`, `:3-statement-model`, `:competitive-analysis`, `:debug-model`, `:ppt-template`.

**Try this:** `/financial-analysis:comps` — build a trading-comps set for Datadog against Snowflake, MongoDB, and Cloudflare.

#### investment-banking

**What it does:** Sell-side and M&A execution: anonymous teasers, CIMs, buyer universes, accretion/dilution merger models, process letters, branded one-page strip profiles, and a live deal-pipeline tracker.

**How to use:** Commands: `/investment-banking:teaser`, `:cim`, `:buyer-list`, `:merger-model`, `:process-letter`, `:one-pager`, `:deal-tracker`.

**Try this:** `/investment-banking:buyer-list` — build a buyer universe for a $40M-ARR healthcare-scheduling SaaS company, split into strategics and sponsors.

#### equity-research

**What it does:** Sell-side research workflows: quarterly earnings analysis and previews, initiating-coverage reports, 7am morning notes, sector overviews, stock screens, thesis tracking, model updates, and a catalyst calendar.

**How to use:** Commands: `/equity-research:earnings`, `:earnings-preview`, `:initiate`, `:morning-note`, `:sector`, `:screen`, `:thesis`, `:model-update`, `:catalysts`.

**Try this:** `/equity-research:earnings` — analyze Microsoft's latest quarter against consensus and draft an earnings update note.

#### private-equity

**What it does:** Sourcing through value creation: inbound deal screening, due-diligence checklists and meeting prep, IC memos, IRR/MOIC sensitivity tables, unit-economics (LTV/CAC, cohorts, retention), portfolio monitoring, AI-readiness scans, and 100-day value-creation plans.

**How to use:** Commands: `/private-equity:source`, `:screen-deal`, `:dd-checklist`, `:dd-prep`, `:ic-memo`, `:returns`, `:unit-economics`, `:portfolio`, `:value-creation`, `:ai-readiness`.

**Try this:** `/private-equity:screen-deal` — screen this CIM against our criteria (lower-middle-market industrials, >$5M EBITDA, >15% margins) and give a pass/fail with reasons. *(attach the CIM)*

#### wealth-management

**What it does:** Advisory workflows: client-review prep, client-facing performance reports, comprehensive financial plans, prospect proposals, portfolio rebalancing with tax awareness, and tax-loss harvesting.

**How to use:** Commands: `/wealth-management:client-review`, `:client-report`, `:financial-plan`, `:proposal`, `:rebalance`, `:tlh`.

**Try this:** `/wealth-management:client-review` — prep for my review with the Henderson household: YTD performance, allocation drift, and three talking points.

#### fund-admin

**What it does:** Fund accounting and finance-ops skills (no slash commands — just describe the task): GL-to-subledger reconciliation, root-cause break tracing, period-end accrual schedules, account roll-forwards, variance/flux commentary, and LP-statement NAV tie-out.

**How to use:** No commands — describe the task and the matching skill activates.

**Try this:** Reconcile the GL to the custodian subledger for trade date 5/20 and classify each break by likely cause.

#### operations

**What it does:** Client-onboarding skills (no slash commands): parse an onboarding packet into structured KYC fields (identity, ownership, control, source of funds) and apply the firm's KYC/AML rules grid to assign a risk rating and flag gaps.

**How to use:** No commands — hand over the documents and describe what you need.

**Try this:** Parse this onboarding packet into KYC fields, then run our rules grid and flag anything missing or escalation-worthy. *(attach the docs)*

### Named agents

Autonomous, multi-step workers. Give each one a goal and it runs the full workflow — gathering data, building models, and drafting documents — bundling its own skills.

#### pitch-agent

**What it does:** End-to-end investment-banking pitch builder. From a target company and a strategic situation, it pulls comps and precedent transactions, builds a DCF and football-field valuation in Excel, and generates a branded pitch deck.

**How to use:** Dispatch with a target company and the situation.

**Try this:** Pitch Agent: build a sell-side pitch for Acme Logistics, which is exploring strategic alternatives — comps, precedents, a DCF and football field, then assemble the deck.

#### market-researcher

**What it does:** Turns a sector or theme into a research note: industry overview, competitive landscape, peer trading comps, and a thematic ideas shortlist, with optional slides.

**How to use:** Dispatch with a sector or theme.

**Try this:** Market Researcher: give me a primer on the data-center cooling sector — landscape, key players, peer comps, and a shortlist of investable ideas.

#### earnings-reviewer

**What it does:** Processes an earnings event end to end: reads the call transcript and filings, updates the coverage model, and drafts the post-earnings note. Works for one name or fanned across a coverage list.

**How to use:** Dispatch when a covered name reports.

**Try this:** Earnings Reviewer: Salesforce just reported — read the call and filing, update our model, and draft the earnings note.

#### meeting-prep-agent

**What it does:** Builds a briefing pack before a client or prospect meeting: relationship history from CRM, current holdings and recent activity, market context, and a suggested agenda.

**How to use:** Dispatch ahead of a client meeting; pairs with a calendar event.

**Try this:** Meeting Prep: I'm meeting the Calloway family office Thursday — assemble a briefing pack with relationship history, holdings, recent activity, and an agenda.

#### model-builder

**What it does:** Builds DCF, LBO, three-statement, and trading-comps models live in Excel from a ticker and an assumption set. Use for a clean model from scratch.

**How to use:** Dispatch with a ticker and assumptions.

**Try this:** Model Builder: build a 3-statement model and DCF for Shopify (SHOP), 5-year forecast, revenue growth 8% tapering to 3%.

#### gl-reconciler

**What it does:** Reconciles general ledger to subledger across asset classes for a trade date, finds breaks, traces root cause, and routes the exception report for sign-off. For daily or month-end recon runs.

**How to use:** Dispatch with a trade date and scope.

**Try this:** GL Reconciler: run the recon for trade date 5/20 across equities and fixed income, trace the breaks, and prepare the exception report for sign-off.

#### kyc-screener

**What it does:** Parses an onboarding document packet, runs the firm's KYC/AML rules engine, screens against sanctions and PEP lists, and flags gaps for escalation. For onboarding or periodic refresh.

**How to use:** Dispatch with the onboarding documents.

**Try this:** KYC Screener: process this new-client packet, run our rules engine, screen for sanctions and PEP, and flag anything needing escalation. *(attach docs)*

#### valuation-reviewer

**What it does:** Ingests GP valuation packages for a fund, runs them through the valuation template, and stages LP reporting. For quarter-end portfolio valuation review.

**How to use:** Dispatch with the GP packages for the fund.

**Try this:** Valuation Reviewer: ingest the Q1 GP valuation packages for Fund III, run them through our template, and stage the LP reporting.

#### month-end-closer

**What it does:** Runs the month-end close for an entity — accruals, roll-forwards, and variance commentary — and stages the close package for controller sign-off.

**How to use:** Dispatch with the entity and period.

**Try this:** Month-End Closer: run the April close for the management company — accruals, roll-forwards, and flux commentary — then stage the package for sign-off.

#### statement-auditor

**What it does:** Audits a batch of pre-generated LP capital-account statements against the fund NAV pack before distribution: ties out balances, allocations, and fees, and flags discrepancies.

**How to use:** Dispatch as the final check before statements go out.

**Try this:** Statement Auditor: audit the Q1 LP statements against the NAV pack and flag any tie-out discrepancies before we distribute.

### Partner plugins

Analytics powered by partner data. Each requires the relevant data entitlement from the partner.

#### lseg (LSEG)

**What it does:** Fixed income, rates, FX, and options analytics on LSEG data: bond relative value, bond-futures basis with cheapest-to-deliver, swap-curve strategy, FX carry, option volatility and Greeks, macro/rates dashboards, fixed-income portfolio review, and equity research snapshots.

**How to use:** Commands: `/lseg:analyze-bond-rv`, `:analyze-bond-basis`, `:analyze-swap-curve`, `:analyze-fx-carry`, `:analyze-option-vol`, `:macro-rates`, `:research-equity`, `:review-fi-portfolio`.

**Try this:** `/lseg:analyze-fx-carry` — evaluate a long-MXN / short-JPY carry trade with current spot, forwards, and the vol surface.

#### sp-global (S&P Global)

**What it does:** Company and market intelligence on S&P Capital IQ data via the Kensho LLM-ready API: company tear sheets, single-name earnings previews, and a one-page funding-activity digest slide.

**How to use:** No commands — ask in natural language; skills fire.

**Try this:** Generate an S&P Global tear sheet for Caterpillar (CAT), then build an earnings preview for Delta Air Lines.

### Admin tooling

#### claude-for-msft-365-install

**What it does:** Provisions the Claude Microsoft 365 add-in (Excel, Word, PowerPoint, Outlook) against your own cloud — Vertex AI, Bedrock, Azure Foundry, or an internal LLM gateway — instead of Anthropic's API. Handles manifest generation, Azure admin consent, and per-user routing config. This is a Claude Code plugin, not a Cowork agent, and is for IT admins only. **Firms using Claude directly from Anthropic's hosted service can skip this** — see [Claude for Microsoft 365](#claude-for-microsoft-365--install-tooling) below.

**How to use:** Admin commands: `/claude-for-msft-365-install:setup` (wizard), `:manifest`, `:consent`, `:bootstrap`, `:update-user-attrs`, `:debug`.

**Try this:** `/claude-for-msft-365-install:setup` — provision the add-in against our Bedrock deployment in us-east-1 and generate the manifest.

## Data connectors (MCP)

All connectors are centralized in the **financial-analysis** core plugin and shared across the rest. MCP access may require a subscription or API key from the provider.

| Provider | URL |
|---|---|
| [Daloopa](https://www.daloopa.com/) | `https://mcp.daloopa.com/server/mcp` |
| [Morningstar](https://www.morningstar.com/) | `https://mcp.morningstar.com/mcp` |
| [S&P Global](https://www.spglobal.com/) | `https://kfinance.kensho.com/integrations/mcp` |
| [FactSet](https://www.factset.com/) | `https://mcp.factset.com/mcp` |
| [Moody's](https://www.moodys.com/) | `https://api.moodys.com/genai-ready-data/m1/mcp` |
| [MT Newswires](https://www.mtnewswires.com/) | `https://vast-mcp.blueskyapi.com/mtnewswires` |
| [Aiera](https://www.aiera.com/) | `https://mcp-pub.aiera.com` |
| [LSEG](https://www.lseg.com/) | `https://api.analytics.lseg.com/lfa/mcp` |
| [PitchBook](https://pitchbook.com/) | `https://premium.mcp.pitchbook.com/mcp` |
| [Chronograph](https://www.chronograph.pe/) | `https://ai.chronograph.pe/mcp` |
| [Egnyte](https://www.egnyte.com/) | `https://mcp-server.egnyte.com/mcp` |

## Making It Yours

These are reference templates — they get better when you tune them to how your firm works.

- **Swap connectors** — point `.mcp.json` at your data providers and internal systems.
- **Add firm context** — drop your terminology, processes, and formatting standards into skill files.
- **Bring your templates** — `/ppt-template` teaches Claude your branded PowerPoint layouts.
- **Adjust agent scope** — edit `agents/<slug>.md` to match how your team actually runs the workflow.
- **Add your own** — copy the structure for workflows we haven't covered.

After editing a skill in a vertical, run `python3 scripts/sync-agent-skills.py` to propagate the change into any agent that bundles it, then `python3 scripts/check.py` to validate. Repackage the plugin (see [Building a zip from source](#building-a-zip-from-source)) and re-upload.

## Repository layout

```
plugins/
  agent-plugins/               # Named agents — one self-contained plugin each
  vertical-plugins/            # Skill + command bundles by FSI vertical, plus MCP connectors
  partner-built/               # Partner-authored plugins (LSEG, S&P Global)
managed-agent-cookbooks/       # Claude Managed Agent cookbooks — one dir per agent
claude-for-msft-365-install/   # Admin tooling to provision the Claude Microsoft 365 add-in
claude-upload-packages/        # Prebuilt, ready-to-upload plugin zips (manual install)
scripts/                       # deploy-managed-agent.sh · check.py · validate.py · orchestrate.py · sync-agent-skills.py
```

## Claude for Microsoft 365 — install tooling

If your firm runs Claude inside Excel, PowerPoint, Word, and Outlook via the Microsoft 365 add-in, [`claude-for-msft-365-install/`](./claude-for-msft-365-install) is the admin tooling to provision it against **your own cloud** — Vertex AI, Bedrock, or an internal LLM gateway — instead of Anthropic's API. It walks an IT admin through generating the customized add-in manifest, granting Azure admin consent, and writing per-user routing config via Microsoft Graph.

**This is only needed for bring-your-own-cloud routing.** Firms that use Claude directly from Anthropic's hosted service can skip it — the standard add-in is deployed through the Microsoft 365 admin center, and the agents and skills here run inside it either way.

## Contributing

Everything here is markdown and YAML — fork, edit, PR.

- New skill → add it under `plugins/vertical-plugins/<vertical>/skills/`, then run `python3 scripts/sync-agent-skills.py` to propagate to any agent that bundles it.
- New agent → `plugins/agent-plugins/<slug>/` (with `agents/<slug>.md` + `skills/`) and a matching `managed-agent-cookbooks/<slug>/`.
- Run `python3 scripts/check.py` before pushing — it lints every manifest, verifies all cross-file references resolve, and fails if any bundled skill has drifted from its vertical source.

## License

[Apache License 2.0](./LICENSE)
