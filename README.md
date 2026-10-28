# Agree.com

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Agree.com is a contract-to-cash platform that combines free, unlimited e-signatures with invoicing, billing and integrated payments, positioning itself directly against DocuSign and Bill.com by giving the signature product away and monetizing the money movement that follows it. The company raised a $7.2M seed round led by Pelion Venture Partners in May 2025 after a $3M pre-seed led by Better Tomorrow Ventures. It markets an "agentic revenue operating system" built from named AI agents for contracts, billing, collections, recovery, reconciliation and insight. For developers it publishes a 56-operation REST API covering agreements, invoices, contacts, customers, cash-flow and recovery reporting, and webhooks, described by a real OpenAPI 3.0 document served anonymously at secure.agree.com/documentation/openapi, plus a hosted remote MCP server at secure.agree.com/mcp discoverable through RFC 9728 protected resource metadata and guarded by an OAuth 2.1 authorization server with PKCE and dynamic client registration.

**Areas:** Agreements · E-Signature · Contract Management · Invoicing · Billing · Payments · Accounts Receivable · Fintech · Financial Services · Webhook · MCP · Agent-Native

## Agree.com on APIs.io

Everything profiled for Agree.com is published on APIs.io rather than in this repository. Start at the provider page — **[apis.io/providers/agree-com/](https://apis.io/providers/agree-com/)** — or go straight to one of the APIs or properties below.

### APIs (6)

- [Agree.com Agreements API](https://apis.io/apis/agree-com/agree-com-agreements-api/) — Create, send, and manage agreements with recipients and field assignments. ## Overview Agreements are documents that require signatures from one or more…
- [Agree.com Contacts API](https://apis.io/apis/agree-com/agree-com-contacts-api/) — Manage your organization's contacts - the people and companies you do business with. ## Overview Contacts are the foundation of your billing workflow.…
- [Agree.com Customers API](https://apis.io/apis/agree-com/agree-com-customers-api/) — The Customers API from Agree.com — 2 operation(s) for customers.
- [Agree.com Invoices API](https://apis.io/apis/agree-com/agree-com-invoices-api/) — Create, send, and track payment requests to your customers. ## Overview Invoices are the core of Agree's payment system. An invoice represents a request for…
- [Agree.com Reports API](https://apis.io/apis/agree-com/agree-com-reports-api/) — Read-only analytics for revenue, cashflow, and accounts receivable recovery. ## Overview Report endpoints mirror the in-app **Reports** dashboards…
- [Agree.com Webhooks API](https://apis.io/apis/agree-com/agree-com-webhooks-api/) — Receive real-time notifications when events occur in your Agree account. ## Overview Webhooks push event data to your application as soon as something…

### Published properties

Each property below is a page on APIs.io carrying the machine-readable artifact and what it says.

#### AsyncAPI (1)

Event-driven and webhook contracts.

- [Agree.com Webhooks](https://apis.io/asyncapis/agree-com/agree-com-webhooks-asyncapi/)

#### Event channels (1)

Individual events and webhooks the provider emits.

- [/](https://apis.io/channels/agree-com/webhookendpoint/)

#### MCP servers (1)

Model Context Protocol servers that expose the API to agents.

- [Agree.com MCP Server](https://apis.io/servers/agree-com/agree-com-mcp-server/)

#### Overlays (1)

OpenAPI overlays, including conversational phrasing for each operation.

- [Agree Com Api Overlay](https://apis.io/overlays/agree-com/agree-com-api-overlay/)

#### Security (2)

Authentication, trust centre, and vulnerability disclosure.

- [Agree Com Authentication](https://apis.io/security/agree-com/agree-com-authentication/)
- [Agree Com Domain Security](https://apis.io/security/agree-com/agree-com-domain-security/)

#### OAuth scopes (1)

The permissions the API asks for.

- [Agree Com Scopes](https://apis.io/scopes/agree-com/agree-com-scopes/)

#### Plans and pricing (1)

Published plans, tiers and prices.

- [Agree Com Plans Pricing](https://apis.io/plans/agree-com/agree-com-plans-pricing/)

#### Rate limits (1)

Published request limits.

- [Agree Com Rate Limits](https://apis.io/rate-limits/agree-com/agree-com-rate-limits/)

## Where to find this API

Links to the company's own pages. This repository publishes no copy of their specifications — the canonical document is theirs and lives at the URLs below.

- [Website](https://agree.com/)
- [Documentation](https://secure.agree.com/documentation)
- [Getting started](https://secure.agree.com/documentation#section/Introduction/Quick-Start)
- [API reference](https://secure.agree.com/documentation)
- [Pricing](https://agree.com/pricing)
- [Sign up](https://secure.agree.com/signup)
- [Terms of service](https://agree.com/terms)
- [Privacy policy](https://agree.com/privacy)

## API Evangelist rating

**Kin Score 52.0** — developing · rubric 0.25.0 · scored 2026-10-23

| Facet | Score |
|---|---|
| Discoverability | 68.3 |
| Contract quality | 61.2 |
| Contract governance | 18.2 |
| Operational transparency | 7.9 |
| Developer ergonomics | 51.8 |
| Access clarity | 76.3 |

**Agent readiness 42.9** — agent-ready (gated down from agent-native)

| Agent-readiness dimension | Result |
|---|---|
| Spec Presence | yes |
| Agentic Access | — |
| Reversibility Documented | — |
| MCP Server | documented (partial credit) |
| Auth Clarity | served (partial credit) |
| Idempotency | — |
| Error Semantics | verified |
| OpenAPI Examples | — |
| Rate Limit Signal | documented (partial credit) |
| Event Surface Described | derived (partial credit) |
| Agent Skills | derived (partial credit) |
| Well Known Catalog | — |
| Consent Identity | — |
| Agent Card | — |
| Dry Run Mode | — |
| Delegated Identity | served (partial credit) |
| Protected Resource Metadata | verified |
| Dynamic Client Registration | yes |
| Agentic Commerce | — |
| Pagination Ratio | — |
| Operation Distinctiveness | — |

**Regulatory alignment 31.3** — Payments

The rubric is published at [github.com/api-evangelist/kin-score](https://github.com/api-evangelist/kin-score). The per-check breakdown behind these numbers — which checks were earned, partial or missed — along with score history and how this provider compares to its peers, is available at [apis.io/providers/agree-com/](https://apis.io/providers/agree-com/).

## How this profile is made

API Evangelist profiles a company's public API surface the way a developer would find it: the website, developer portal and documentation, the specifications it publishes, its public repositories, and its pricing, status and changelog pages. From that it derives the properties above — contracts split per API, workflows, schemas, collections, governance rules, plans and rate limits — and scores them against the published [Kin Score rubric](https://github.com/api-evangelist/kin-score).

The artifacts are published on [APIs.io](https://apis.io), not in this repository. This repository is the public record for Agree.com: the place to say something is wrong, missing or out of date, and to ask for a re-score or a removal.

## Open an issue

- **Something about this listing** — a wrong description, a missing API, a stale artifact or a question about the rating: [open an issue on this repository](https://github.com/api-evangelist/agree-com/issues/new).
- **A new API, or one that is not in the catalog yet** — [submit it on APIs.io](https://apis.io/add/).
- **Anything else** — [the APIs.io inbox](https://github.com/api-search/inbox/issues/new) or [info@apievangelist.com](mailto:info@apievangelist.com).
