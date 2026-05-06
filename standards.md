# Standards & API Reference

> Project: Sales Email Sequencer · Generated: 2026-05-06

## Industry Standards & Specifications

### IETF / RFC Standards

**RFC 5321 — Simple Mail Transfer Protocol (SMTP)**
- URL: https://datatracker.ietf.org/doc/html/rfc5321
- The core protocol for email transmission between mail servers. A sales email sequencer must implement SMTP correctly (including EHLO, STARTTLS, and AUTH extensions) when using custom sending infrastructure or relaying through SMTP providers. Correct SMTP implementation underpins deliverability.

**RFC 5322 — Internet Message Format**
- URL: https://datatracker.ietf.org/doc/html/rfc5322
- Defines the syntax of email messages: headers (From, To, Cc, Bcc, Subject, Date, Message-ID, Reply-To), body encoding, and MIME boundaries. Every sequencer must produce RFC 5322-compliant messages to guarantee interoperability across mail clients.

**RFC 6376 — DomainKeys Identified Mail (DKIM) Signatures**
- URL: https://datatracker.ietf.org/doc/html/rfc6376
- Defines the DKIM email signing mechanism. A sequencer must sign outbound messages with the sender's private key, or guide users through DNS record creation, so that receiving servers can verify domain ownership. Required for inbox placement at Gmail and Outlook in 2026.

**RFC 7208 — Sender Policy Framework (SPF)**
- URL: https://datatracker.ietf.org/doc/html/rfc7208
- Defines SPF, which allows domain owners to publish authorized IP addresses for sending email via DNS TXT records. Sequencers must validate that connected sending accounts have correct SPF records and warn when misconfigurations are detected.

**RFC 7489 — Domain-based Message Authentication, Reporting, and Conformance (DMARC)**
- URL: https://datatracker.ietf.org/doc/html/rfc7489
- Defines DMARC policy enforcement and aggregate reporting (RUA/RUF). Since February 2024, Google and Yahoo require DMARC compliance for bulk senders (5,000+ messages/day). A sequencer must assist users in publishing DMARC DNS records and interpreting aggregate reports to maintain inbox placement.

**RFC 8058 — Signaling One-Click Functionality for List-Unsubscribe**
- URL: https://datatracker.ietf.org/doc/html/rfc8058
- Defines the `List-Unsubscribe-Post` header, enabling one-click unsubscribe from within Gmail and Outlook interfaces. As of February 2024, Google requires this header for all bulk senders. Starting November 2025, Gmail began actively rejecting non-compliant traffic. Every sequencer must insert this header and process POST-based unsubscribe requests within 48 hours.

**RFC 9051 — Internet Message Access Protocol (IMAP) Version 4rev2**
- URL: https://www.rfc-editor.org/rfc/rfc9051.html
- The current IMAP base specification (supersedes RFC 3501). IMAP is essential for reply detection: sequencers poll or use IDLE commands on connected inboxes to identify inbound replies and automatically pause the sequence for a prospect. OAuth 2.0 IMAP access (replacing Basic Auth) is now required by both Google and Microsoft.

**RFC 3464 — Delivery Status Notifications (DSN)**
- URL: https://datatracker.ietf.org/doc/html/rfc3464
- Defines the machine-readable format for email bounce messages (hard bounce, soft bounce, delay notifications). A sequencer must parse DSN messages to categorise bounces, suppress hard-bounced addresses, and track soft-bounce patterns as a deliverability signal.

**RFC 5965 — Abuse Reporting Format (ARF)**
- URL: https://datatracker.ietf.org/doc/html/rfc5965
- Defines the format for spam complaint feedback loops from ISPs (FBL). A sequencer must process ARF messages from feedback loop subscriptions to identify complaints, suppress complaining recipients, and monitor complaint rate trends per sending domain.

**RFC 6531 — SMTP Extension for Internationalized Email**
- URL: https://datatracker.ietf.org/doc/html/rfc6531
- Defines SMTPUTF8 for sending to internationalized (Unicode) email addresses. Relevant for sequencers targeting APAC and EMEA markets where non-ASCII addresses are common.

### W3C Standards

**OAuth 2.0 Framework (RFC 6749) and Bearer Token (RFC 6750)**
- URL: https://datatracker.ietf.org/doc/html/rfc6749 · https://datatracker.ietf.org/doc/html/rfc6750
- The authorization framework used by Gmail API and Microsoft Graph API to delegate mailbox access. As of March 2026 (Google) and September 2025 (Microsoft), Basic Authentication has been permanently disabled; OAuth 2.0 is the only supported mechanism for IMAP, SMTP, and API-based email access.

**OpenID Connect 1.0**
- URL: https://openid.net/specs/openid-connect-core-1_0.html
- Identity layer built on OAuth 2.0. Used by Google Workspace and Microsoft 365 for user authentication flows in sequencer apps. Required for SSO integration in enterprise deployments.

### Data Model & API Specifications

**OpenAPI Specification v3.2.0**
- URL: https://spec.openapis.org/oas/v3.2.0.html
- The current (September 2025) stable version of OAS. Sequencer APIs should expose an OpenAPI 3.2-compliant schema, which adds native webhook documentation, streaming media type support (SSE, JSON Lines), and is fully JSON Schema 2020-12 compatible. Both Outreach and Apollo expose OpenAPI schemas.

**JSON Schema (Draft 2020-12)**
- URL: https://json-schema.org/specification.html
- Standard for validating JSON document structure. Relevant for defining prospect list schemas, sequence event payloads, and webhook bodies. OpenAPI 3.1+ uses JSON Schema 2020-12 natively.

**Standard Webhooks Specification**
- URL: https://www.standardwebhooks.com/ · https://github.com/standard-webhooks/standard-webhooks
- A community specification defining consistent webhook signing and header conventions (`webhook-id`, `webhook-timestamp`, `webhook-signature`) using HMAC-SHA256. Compatible with IETF HTTP Message Signatures, OpenAPI, and CloudEvents. Adopting this standard for outbound webhooks ensures interoperability with CRMs, Zapier, and Make connectors.

**iCalendar (RFC 5545) and CalDAV (RFC 4791)**
- URL: https://datatracker.ietf.org/doc/html/rfc5545
- Standard for embedding meeting invites in emails. Relevant for the meeting-booking step in AI reply agents that autonomously schedule calls.

### Security & Compliance Standards

**OWASP API Security Top 10 (2023)**
- URL: https://owasp.org/www-project-api-security/
- Guidelines for securing REST APIs. A sequencer's API must address broken object-level authorization, injection, and security misconfiguration risks, particularly given that it handles access tokens for connected email accounts.

**NIST SP 800-63B — Digital Identity Guidelines**
- URL: https://pages.nist.gov/800-63-3/sp800-63b.html
- Authentication assurance standards. Relevant for sequencer platforms handling enterprise credentials, including OAuth token storage, session management, and MFA requirements.

**CAN-SPAM Act (US)**
- URL: https://www.ftc.gov/business-guidance/resources/can-spam-act-compliance-guide-business
- Requires: honest subject lines, physical mailing address in every email, a functional opt-out mechanism, and honouring opt-out requests within 10 business days (best practice: 24–48 hours). Violations carry penalties up to $53,088 per email (updated January 2025). Sequencer must enforce suppression lists and unsubscribe processing automatically.

**GDPR Article 6 — Lawful Basis for Processing (EU)**
- URL: https://gdpr.eu/article-6-how-to-process-personal-data-legally/
- Requires explicit, freely given consent for marketing emails to EU residents, or documented legitimate interest assessment for B2B cold outreach. Sequencers targeting EU prospects must provide consent logging, data deletion workflows, and configurable legal basis documentation. GDPR fines can reach €20M or 4% of global annual turnover.

**CASL — Canada's Anti-Spam Legislation**
- URL: https://crtc.gc.ca/eng/internet/anti.htm
- Opt-in consent required for commercial messages to Canadian recipients. Stricter than CAN-SPAM; implied consent has a limited time window (2 years). Sequencer must support per-contact consent status tracking.

**Google Bulk Sender Guidelines (2024–2026)**
- URL: https://support.google.com/a/answer/81126
- Enforceable requirements for senders of 5,000+ messages/day to Gmail: DKIM alignment, SPF, DMARC policy of at least `p=none`, one-click unsubscribe (RFC 8058), and spam rate below 0.30% in Postmaster Tools. Active enforcement with rejections began November 2025.

### MCP Server Specifications

**Model Context Protocol (MCP)**
- URL: https://modelcontextprotocol.io/
- Anthropic's open protocol for connecting LLMs to external data sources and tools. Relevant for an AI-native sequencer that exposes sequence management, prospect data, and reply context as MCP tools — enabling LLM agents (Claude, GPT-4o) to autonomously write and optimise sequences, classify replies, and trigger follow-up steps. An MCP server wrapper over the sequencer's REST API would be a differentiating integration for the AI-first market.

---

## Similar Products — Developer Documentation & APIs

### Outreach

- **Description:** Enterprise sales engagement platform with multi-channel sequencing, AI reply classification, and deep Salesforce integration. Dominant in the mid-market and enterprise segment.
- **API Documentation:** https://developers.outreach.io/api/
- **API Reference (OpenAPI):** https://api.outreach.io/api/v2/schema/openapi.json
- **Developer Portal:** https://developers.outreach.io/
- **OAuth Guide:** https://developers.outreach.io/api/oauth/
- **Standards:** REST/JSON; OpenAPI 3.x schema published; JSON:API-compatible response envelopes
- **Authentication:** OAuth 2.0 (Authorization Code flow); self-serve credential generation via developer portal

### Salesloft

- **Description:** Sales engagement platform with cadence management, call coaching, and conversation intelligence. Acquired Drift (2023); competes directly with Outreach.
- **API Documentation:** https://developers.salesloft.com/docs/platform/intro/
- **Developer Portal:** https://developers.salesloft.com/
- **Postman Collection:** https://www.postman.com/salesloft-dev/salesloft/documentation/u1411ak/salesloft-developer-api
- **Standards:** REST/JSON
- **Authentication:** OAuth 2.0 (Authorization Code and Client Credentials flows); access tokens used as Bearer headers

### Apollo.io

- **Description:** All-in-one prospecting and sequencing platform with a 275M+ contact database, email sequencing, and AI writing tools.
- **API Documentation:** https://docs.apollo.io/
- **API Reference:** https://docs.apollo.io/docs/api-overview
- **Base URL:** https://api.apollo.io/api/v1
- **Standards:** REST/JSON; language-agnostic (Python, JavaScript, Ruby, Java, PHP all documented)
- **Authentication:** API key (header-only as of September 2024); OAuth 2.0 for approved partners

### Instantly

- **Description:** Purpose-built cold email platform with multi-inbox rotation, automatic warm-up, and an AI Reply Agent. Flat-fee pricing; deliverability-first.
- **API Documentation:** https://developer.instantly.ai/
- **Webhook Reference:** https://developer.instantly.ai/api/v2/webhook
- **Base URL:** https://api.instantly.ai/
- **Standards:** REST/JSON; interactive "Try it" console in docs
- **Authentication:** Bearer token (API key); granular API scopes; multiple revocable keys
- **Webhook Events:** reply received, email sent, email opened, interest label applied; POST with JSON body
- **Plan Requirement:** API V2 requires Growth plan; webhooks require Hyper Growth plan

### Smartlead

- **Description:** High-volume cold email platform with multi-inbox rotation, white-label options, and agency-focused features. Popular with outreach agencies.
- **API Documentation:** https://api.smartlead.ai/reference/references
- **Getting Started:** https://api.smartlead.ai/docs/getting-started
- **Webhook Guide:** https://api.smartlead.ai/guides/webhook-integration
- **Base URL:** https://server.smartlead.ai/api/v1/
- **Standards:** REST/JSON; API key query parameter authentication (no OAuth)
- **Authentication:** API key as query parameter (`api_key`)
- **Webhook Events:** EMAIL_SENT, EMAIL_OPENED, EMAIL_REPLIED, EMAIL_BOUNCED, LEAD_UNSUBSCRIBED; retry with exponential backoff (3 attempts)

### Lemlist

- **Description:** Personalisation-focused multichannel sequencer with dynamic personalised images, video thumbnails, custom landing pages, and LinkedIn outreach.
- **API Documentation:** https://developer.lemlist.com/
- **API Reference:** https://developer.lemlist.com/api-reference/getting-started/overview
- **Base URL:** https://api.lemlist.com/api
- **Standards:** REST/JSON
- **Authentication:** API key; rate limit of 20 requests per 2 seconds per key
- **SDKs/Libraries:** Node.js (Axios-based examples); n8n native node; Make (Integromat) connector

### Reply.io

- **Description:** AI SDR platform featuring Jason AI agent for autonomous prospecting, sequence writing, reply management, and meeting booking. Supports email, LinkedIn, WhatsApp, SMS, and phone.
- **API Documentation:** https://apidocs.reply.io/
- **Standards:** REST/JSON; V1 and V2 APIs available; both can be used in parallel
- **Authentication:** API key (from Settings > API Key in account); no OAuth
- **Note:** The platform also markets its email sending functionality as a standalone API product for embedding sequencing into other applications.

### Gmail API (Google Workspace)

- **Description:** Google's official API for reading, sending, and managing Gmail messages. Required for OAuth-based email sending since Basic Authentication was permanently disabled March 2026.
- **API Documentation:** https://developers.google.com/workspace/gmail/api/
- **Send Email Reference:** https://developers.google.com/workspace/gmail/api/reference/rest/v1/users.messages/send
- **OAuth 2.0 Guide:** https://developers.google.com/identity/protocols/oauth2
- **Standards:** REST/JSON; MIME message encoded as base64url in request body
- **Authentication:** OAuth 2.0 (Authorization Code or Service Account); scopes: `gmail.send`, `gmail.readonly`, `gmail.modify`
- **Key Endpoint:** `POST https://gmail.googleapis.com/gmail/v1/users/{userId}/messages/send`

### Microsoft Graph API (Outlook / Microsoft 365)

- **Description:** Unified REST API for Microsoft 365 services including Outlook email read/send/search and real-time webhook change notifications. Mandatory since Basic Authentication was permanently disabled September 2025; EWS will be disabled October 2026.
- **API Documentation:** https://learn.microsoft.com/en-us/graph/api/overview
- **Email Guide:** https://www.unipile.com/microsoft-graph-api-email-integration-guide/
- **OAuth / IMAP Guide:** https://learn.microsoft.com/en-us/exchange/client-developer/legacy-protocols/how-to-authenticate-an-imap-pop-smtp-application-by-using-oauth
- **Standards:** REST/JSON; OData query parameters; webhook change notifications via subscriptions
- **Authentication:** OAuth 2.0 via Azure Active Directory (delegated and application permissions); Client Credentials flow supported for background services
- **Key Endpoints:** `POST /v1.0/me/sendMail`; `GET /v1.0/me/messages`; `POST /v1.0/subscriptions` for change notifications (inbox webhooks)

### HubSpot CRM API

- **Description:** HubSpot's CRM API for contact, deal, company, and activity management. The most commonly required CRM integration for SMB-focused sequencers.
- **API Documentation:** https://developers.hubspot.com/docs/api/overview
- **Developer Portal:** https://developers.hubspot.com/
- **Python SDK:** https://github.com/HubSpot/hubspot-api-python
- **Node.js SDK:** https://github.com/HubSpot/hubspot-api-nodejs
- **Base URL:** https://api.hubapi.com
- **Standards:** REST/JSON; predictable, descriptive URL structures; HubSpot API keys sunset — OAuth only
- **Authentication:** OAuth 2.0 (Authorization Code); Bearer token in HTTP header; API key access permanently discontinued

### Salesforce REST API

- **Description:** Salesforce's REST API for CRM record CRUD, SOQL queries, and workflow automation. Essential integration for enterprise sequencer deployments where Salesforce is the system of record.
- **API Documentation:** https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/
- **Current Guide (Spring '26, v66.0):** https://resources.docs.salesforce.com/latest/latest/en-us/sfdc/pdf/api_rest.pdf
- **OAuth Reference:** https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_oauth_and_connected_apps.htm
- **Standards:** REST/JSON; SOQL for querying; supports bulk API for high-volume record operations
- **Authentication:** OAuth 2.0 via Connected Apps (Spring '26: new connected apps replaced by External Client Apps); Authorization Code and Client Credentials flows

---

## Notes

**OAuth 2.0 as the universal email access standard:** Both Google (March 2026) and Microsoft (September 2025) have fully disabled Basic Authentication. Any sequencer built today must implement OAuth 2.0 for IMAP, SMTP, and API-based email operations. Legacy tools still using Basic Auth (username + password) will fail to connect to Gmail and Outlook accounts.

**EWS deprecation deadline:** Microsoft will disable Exchange Web Services (EWS) for Exchange Online in October 2026. Sequencers that use EWS for Outlook inbox monitoring must migrate to Microsoft Graph API change notifications (webhooks) before this date.

**DMARC enforcement is active:** Google and Yahoo have enforced DMARC alignment since February 2024 for bulk senders. Starting November 2025, Gmail began actively rejecting non-compliant traffic. An AI-native sequencer should automate DMARC setup, monitor aggregate reports, and alert users when their domain reputation degrades.

**RFC 8058 one-click unsubscribe is now mandatory:** Gmail enforcement of the `List-Unsubscribe-Post` header is active. Sequencers that omit this header risk deliverability degradation. Processing unsubscribe requests within 48 hours is the practical requirement.

**No formal sequencing protocol standard exists:** The sales email sequencing category has no ISO or W3C standardised data model for sequence definitions, step conditions, or prospect enrollment. This is an opportunity: an open-source sequencer that publishes an open schema (OpenAPI 3.2 + JSON Schema) for sequence definitions would enable ecosystem interoperability that no current vendor provides.
