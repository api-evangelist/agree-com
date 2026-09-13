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

Agree.com is a contract-to-cash platform that pairs free, unlimited e-signatures with
invoicing, billing and integrated payments (ACH, card, wire), monetizing money movement rather
than signatures. It raised a $7.2M seed round led by Pelion Venture Partners in May 2025 after
a $3M pre-seed led by Better Tomorrow Ventures, and markets an "agentic revenue operating
system" of named AI agents for contracts, billing, collections, recovery, reconciliation and
insight.

## What this profile found

- **A real OpenAPI 3.0 contract** — 37 paths, 56 operations, 44 schemas, 6 tags, every
  operation carrying a unique operationId, summary and description. It is served anonymously
  at `https://secure.agree.com/documentation/openapi`, discovered from the `spec-url`
  attribute of the Redoc page at `https://secure.agree.com/documentation`.
- **A live hosted MCP server** at `https://secure.agree.com/mcp`, found by probing RFC 9728
  protected-resource metadata. It is not documented anywhere on the site — `agree.com/developers`
  advertises MCP as a one-line tile with no endpoint. The server is OAuth-gated (`tools/list`
  returns 401), fronted by an OAuth 2.1 authorization server with PKCE S256, dynamic client
  registration and a single `mcp` scope.
- **A documented base URL that does not exist.** Agree's own documentation states "All API
  requests should be made to: `https://api.agree.com/api/v1`" and every curl example uses that
  host. `api.agree.com` has no DNS record. The working base is the OpenAPI `servers[]` entry,
  `https://secure.agree.com`.
- **No idempotency on any of 25 mutating operations**, including
  `POST /api/v1/invoices/create_and_send`, which creates an invoice and emails a payment link
  in one irreversible call.
- **No refund operation.** The `refunded` invoice status and the `invoice.refunded` webhook
  both exist, but no refund, void or reversal endpoint appears anywhere in the API.
- **A complete webhook catalog** — 12 events, HMAC-SHA256 signed, five-attempt exponential
  backoff — captured as a generated AsyncAPI 3.0 document.
- **No SDKs, no CLI, no GitHub organization, no status page, no changelog, no deprecation
  policy, and no published compliance certifications.**

See `apis.yml` for the full artifact index.

- https://agree.com/
- https://agree.com/developers
- https://secure.agree.com/documentation
