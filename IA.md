# Clubs docs: information architecture

Plan for docs.theprivateclubs.com, the docs for the Clubs MCP and the Clubs REST API. It mirrors the structure of docs.rainbrand.com. Written 2026-10-18 on branch `feat/clubs-docs`. Nothing here is published.

- **App source of truth (read only):** `/Users/mrcjk/Desktop/Projects/Project - theprivateclubs.com/repo-mcp-page` (main, at d49092d4 or later)
- **Live checks:** `curl` against https://www.theprivateclubs.com (setup.md, mcp.json, openapi.json, llms.txt, /api/v1/clubs, /api/mcp)
- **Rainbrand reference copies:** `/private/tmp/claude-501/-Users-mrcjk-Desktop-Cursor-Projects-Project---theprivateclubs-com/436f968b-168e-466a-b181-7a497f24c2be/scratchpad/rb/`

Already applied: `docs.json` (section 6) and the removal of 27 retired pages with `git rm` (section 7). The four writing groups (section 8) write every new page.

---

## 1. Decisions in one place

1. **Two tabs, like Rainbrand:** Documentation (icon `book`) and API reference (icon `brackets-angle`). The tab label is sentence case, per house style.
2. **Only the free anonymous server is documented:** `https://www.theprivateclubs.com/api/mcp`. Sign in with Clubs (`/mcp`) is built but switched off: leave it out. Also leave out the 19 planned tools, fee bands over MCP, saves, enquiries, events, homes and network tools.
3. **One REST endpoint is documented:** `GET /api/v1/clubs`. Do not document `/ai/clubs.json` (returns zero clubs), `/api/clubs` (legacy alias, its spec numbers are wrong), `/api/clubs/{slug}` (not in the spec, not under v1) or `/llms-full.txt` (stale).
4. **The endpoint page will be generated** by Mintlify from the live spec at https://www.theprivateclubs.com/openapi.json, limited to `GET /api/v1/clubs`, once the spec fixes in the app (section 5) ship. Until the `API endpoints` group is added to docs.json, link `/api/openapi` only, never a generated endpoint page.
5. **Guides by audience and use cases by audience.** Guides are how-tos: steps, parameters and MCP or API tabs. Use cases follow Rainbrand's template: the asks, a prompt to adapt, how it works, and checks before you share. Both use only the 15 live asks in `MCP_USE_CASES` (`live: true`).
6. **No hero images in v1.** We have no docs images, and nobody makes any up. Cards use Tabler icons instead.
7. **No hard-coded counts** of clubs, countries or connections. Live figures change hourly (`/mcp` and `/llms.txt` show them).
8. **Data license.** Calls cost nothing and need no account. Under https://www.theprivateclubs.com/data/license, free use is non-commercial with attribution. Commercial use, bulk data access and API integrations beyond the public endpoints need a commercial license. Every guide and use case links the license. Open item 1 in section 10 covers the gap with the "Free" chips on `/mcp`.

---

## 2. Rules for every writer

### Truth

- Every sentence must be true today. Check it against the app source or a live `curl` before you write it. If you cannot verify it, leave it out.
- Never invent features, prices, limits, numbers, endpoints, fields, UI labels or vendor steps.
- Fees: the MCP server never returns fees, and API calls from a server or script never return fee figures. Never state, estimate or imply a club's fees. Never paste fee figures.
- Rate limits: every REST response carries static `RateLimit` and `RateLimit-Policy: 120;w=60` headers. No code counts requests or returns 429, and `/api/mcp` has no request limit. Describe the headers as the advertised policy. Never claim enforcement and never invent a 429 body.
- REST has no CORS headers, so cross-origin browser calls fail. Tell readers to call it from a server. The MCP server allows any origin.
- Do not paste real club `description` text: some of it is third-party editorial. Leave the field out of examples or say it is omitted.
- Example output must come from a live call made on the day you write. Trim fields; never edit values. Avoid quoting `total` values as fixed facts.
- Do not mention "coming soon", "planned" or "not yet" features. Leave them out.
- Do not repeat the two UNCONFIRMED lines in `src/lib/mcp/setup.ts`: the ChatGPT workspace requirement and Cursor's "about 40 tools" note.

### Naming

- The MCP and API are **Clubs**: "the Clubs MCP", "the Clubs MCP server", "the Clubs API". The display name is Clubs and the server key is `clubs`.
- The company and site are **Private Clubs**. Never write "The Private Clubs", "ThePrivateClubs.com" or "TPC". The domain stays theprivateclubs.com, and you can write "theprivateclubs.com" when you mean the site.
- "Agent" on its own means an AI agent. The audience is "real estate agents", always in full.
- Tool names go in code font: `search_clubs`, `get_club`, `get_clubs_by_state`, `compare_clubs`, `get_membership_info`.

### House style

- No em dashes anywhere, and no middle dots. Restructure the sentence instead.
- Headings, buttons, card titles and tab labels are sentence case. Title Case is for chips or badges only.
- Short, plain sentences (aim for 13 to 14 words on average), second person, imperative for tasks. No exclamation marks.
- Each page's frontmatter `description` is one sentence with no closing full stop (the house rule for sub lines). Body prose uses normal punctuation.
- UI paths are bold, with `>` between steps, as the site writes them: **Customize > Connectors**.
- End every page with a short "Next" line or list that links the next pages.

### Mintlify conventions

- Files are `.mdx` with YAML frontmatter: `title`, `description`, and `sidebarTitle` or `mode: "wide"` where this IA says so. Do not add a body H1, because Mintlify renders the title.
- Use the components Rainbrand uses: `Card`, `CardGroup`, `Steps`/`Step`, `Tabs`/`Tab`, `Note`, `Columns`, and, rarely, `Accordion` or `Info`. Do not use `Tip`, `Warning`, `CodeGroup`, `ParamField` or `ResponseField`.
- Icons come from the Tabler library (set in docs.json). Use only these names: `book`, `brackets-angle`, `plug`, `download`, `external-link`, `terminal-2`, `search`, `users`, `building`, `sparkles`, `briefcase`, `home`, `file-text`, `lifebuoy`.
- Code fences always carry a language: `bash` for curl and terminal commands, `text` for prompts a reader pastes into an AI app, `json`, `yaml`, and `mermaid`.
- Internal links are root-relative with no extension: `/mcp/connect`, `/api` (the API quickstart file `api/index.mdx`), `/reference/club-object`, `/mcp/connect#another-app`. Anchors follow the H2 text in this IA.
- Link `/api/openapi` only until the `API endpoints` group is added to docs.json (section 5). After that, link the generated endpoint page only from `api/openapi.mdx`, at `/api-reference/clubs/search-clubs`, the path the section 5 spec fix pins. Everywhere else, link `/api/openapi`.

### Standard lines (copy exactly)

| Use | Text |
|---|---|
| Address | `https://www.theprivateclubs.com/api/mcp` |
| Transport | Streamable HTTP |
| Server key and name | key `clubs`, display name Clubs |
| Free line | Free, no account or API key needed |
| Setup line (paste into an AI app) | `Set up the Clubs MCP for me: read https://www.theprivateclubs.com/mcp/setup.md and follow the steps for this app` |
| Check prompt | `Search clubs in Florida` |
| First ask | `Find yacht clubs in Florida` |
| Older entry | Added `private-clubs` before? It is the same server under its old name. Replace it with `clubs`. |
| Fees | Clubs never returns membership fees. Account holders see fees on each club's profile at theprivateclubs.com. |
| Fees (pages that cover the API too) | The Clubs MCP server never returns membership fees, and API calls from your server or script return none. Account holders see fees on each club's profile at theprivateclubs.com. |
| Tool fee message (quote only as tool output) | `Membership fees are available to account holders at theprivateclubs.com. Members see exact verified fees.` |
| Attribution | Cite theprivateclubs.com and link the club's profile. See the [data license](https://www.theprivateclubs.com/data/license) for commercial use. |
| Help | Email support@theprivateclubs.com |
| Setup page | https://www.theprivateclubs.com/mcp |

---

## 3. Navigation

### Tab: Documentation (icon `book`)

| Group | Order | File | Title | sidebarTitle | Notes |
|---|---|---|---|---|---|
| Getting started | 1 | `introduction.mdx` | Introduction | | rewrite in place |
| | 2 | `how-clubs-works.mdx` | How Clubs works | | new |
| | 3 | `api/index.mdx` | API quickstart | | new, served at `/api` |
| | 4 | `mcp/getting-started.mdx` | MCP quickstart | | new |
| Guides | 1 | `guides/members.mdx` | Find and compare clubs | For members | new |
| | 2 | `guides/clubs.mdx` | Check your club's listing | For clubs | new |
| | 3 | `guides/brands-and-creators.mdx` | Build a list of clubs | For brands and creators | new |
| | 4 | `guides/real-estate-agents.mdx` | Look up the club behind a listing | For real estate agents | new |
| Use cases | 1 | `use-cases.mdx` | Use cases | Overview | new, `mode: "wide"` |
| | 2 | `use-cases/members.mdx` | Shortlist clubs to join | Members | new |
| | 3 | `use-cases/clubs.mdx` | See how your club reads in AI apps | Club teams | new |
| | 4 | `use-cases/brands-and-creators.mdx` | Map clubs by type and region | Brands and creators | new |
| | 5 | `use-cases/real-estate-agents.mdx` | Answer club questions for buyers | Real estate agents | new |
| | 6 | `use-cases/home-buyers.mdx` | Check the club before you buy a home | Home buyers | new |
| MCP | 1 | `mcp/connect.mdx` | Install Clubs | | new |
| | 2 | `mcp/tools.mdx` | Available tools | | new |
| | 3 | `mcp/troubleshooting.mdx` | Troubleshooting | | new |

### Tab: API reference (icon `brackets-angle`)

| Group | Order | File | Title | Notes |
|---|---|---|---|---|
| API | 1 | `api/openapi.mdx` | API overview | new |
| | 2 | `api/authentication.mdx` | Authentication and fees | rewrite in place |
| | 3 | `api/usage-limits.mdx` | Pricing and limits | new |
| Data reference | 1 | `reference/club-object.mdx` | Club object | new |
| | 2 | `reference/club-types.mdx` | Club types | new |
| | 3 | `reference/conventions.mdx` | Conventions | new |
| | 4 | `reference/discovery-files.mdx` | Discovery files | new |
| API endpoints | 1 | generated: `GET /api/v1/clubs` | from the spec summary | held back until the section 5 fixes ship |

That makes 24 hand-written pages. The generated page is held back.

---

## 4. Page specs

Word counts are prose words, excluding code. Facts cite the source a writer should check. Section 9 collects the shared facts.

### 4.1 Getting started

#### `introduction.mdx`: Introduction

- **description:** Search and compare private clubs from your AI app or your own product, free and without an account
- **Length:** 200 to 300 words. **Components:** `CardGroup cols={2}` with 2 `Card`s.
- **Outline:**
  - Opening, in two short paragraphs. Clubs is the MCP server and REST API from Private Clubs. It serves the public directory at theprivateclubs.com: private membership clubs worldwide, with profile, course, amenities, waitlist status and a link to each club's profile. Use the free line. Then the fees line.
  - `## Start using Clubs`: a CardGroup with "Connect an AI app" (icon `plug`, href `/mcp/getting-started`, body: install Clubs in Claude, ChatGPT, Cursor or another AI app) and "Build with the API" (icon `brackets-angle`, href `/api`, body: call `GET /api/v1/clubs` from your server).
  - `## What a club record holds`: define **club record** in bold on first use as the fields Clubs returns for one listed club. Link `/reference/club-object` and `/use-cases`.
  - `## Get help`: the help line.
- **Leave out:** counts, "North America", Windsurf, the old Platform, Members, Clubs and Pricing content, and sign-in.

#### `how-clubs-works.mdx`: How Clubs works

- **description:** One clubs directory, served to AI apps through MCP and to your product through the REST API
- **Length:** 300 to 450 words. **Components:** one `mermaid` flowchart LR and one `Note`.
- **Outline:**
  - Opening paragraph: what flows where.
  - Mermaid: `Club listings on theprivateclubs.com` to `Clubs MCP server` and `Clubs REST API`, then to `AI apps and your product`, then to `Answers with profile links`.
  - `## Choose MCP or the API`: adapt the `/mcp` FAQ answer (`src/app/(marketing)/mcp/mcp-faq-content.ts`). MCP connects Clubs to compatible agents. The REST API lets you integrate Clubs into an application, backend or automated workflow. Both read the same directory. Name the 5 tools inline and the one endpoint.
  - `## What comes back`: listed public clubs only (drafts and quarantined rows never appear). Profile basics, course data (holes, par, yardage, architect), amenities and waitlist status. MCP results carry a `url` to the club's profile; REST rows do not include a profile link.
  - `## How fees work`: the fees line. API responses carry `fee_access`, which is `locked` for every API call. Link `/api/authentication`.
  - `## Keep a listing current`: a club can claim its listing free at https://www.theprivateclubs.com/list, with a work email and no credit card (live `/list`). Link `/guides/clubs`.
  - `Note`: "Clubs is the name of the MCP server and API. Private Clubs is the company and the site at theprivateclubs.com."

#### `api/index.mdx`: API quickstart

- **description:** Make your first request to the Clubs API in five steps
- **Length:** 450 to 700 words, 5 to 7 code blocks. Use numbered H2s, as Rainbrand does, not `Steps`.
- **Outline:**
  - Intro line: no key, no account; call it from your server, because the API sends no CORS headers.
  - `## 1. Send your first request`: `curl "https://www.theprivateclubs.com/api/v1/clubs?type=golf&state=Arizona&limit=5"` (verified live, returns rows).
  - `## 2. Read the response`: body `{clubs, total, fee_access}`, with a trimmed live JSON sample of 1 club, no `description`. Point out `fee_access: "locked"`, null fee fields and `fee_views` kinds `locked` or `none`. Headers: `X-Total-Clubs`, `Cache-Control: private, no-store`. Link `/reference/club-object`.
  - `## 3. Narrow by place and type`: `type` uses grouped categories (link `/reference/club-types`). `state` takes a full name or a two-letter code (`FL` and `Florida` return the same clubs). `country` takes a name or ISO code (`United Kingdom` and `GB` match the same clubs). With no country the search is worldwide, but a state with no country stays in the US and Canada. `q` matches part of the club name. `waitlist` takes a comma-separated list of `open`, `waitlist` and `by_invitation`. Document only these filters (see 9.3).
  - `## 4. Page through results`: `page` starts at 1. `limit` defaults to 50; values above 200 are clamped to 200. Stop when `page * limit >= total`. Show a short bash loop.
  - `## 5. Handle errors`: `application/problem+json` with `type`, `title`, `status`, `detail`, `code`, `hint`, `instance`. Show the live `invalid_limit` body from `curl ".../api/v1/clubs?limit=abc"`. Link `/api/openapi#errors`.
  - `## Next steps`: links to `/api/openapi`, `/api/authentication`, `/reference/club-object`, `/reference/club-types`.
- **Leave out:** `/ai/clubs.json`, `/api/clubs`, fee filters (`feeMin`, `feeMax`), rate-limit enforcement and browser calls.

#### `mcp/getting-started.mdx`: MCP quickstart

- **description:** Connect Clubs to your AI app and ask your first question
- **Length:** 300 to 450 words. **Components:** `CardGroup cols={3}` with 3 download `Card`s, one `Note`, and `text` prompt blocks.
- **Outline:**
  - Intro line: install Clubs, check it works, then ask about clubs. Use the free line.
  - `## Install Clubs`: start with the setup line in a `text` block. "Agent apps such as Claude Code, Codex and Cursor run the setup for you. Chat apps walk you through the clicks." Then a CardGroup of 3 download Cards (icon `download`):
    - "Claude and Claude Code": https://www.theprivateclubs.com/mcp/clubs.claude-plugin.zip
    - "Codex": https://www.theprivateclubs.com/mcp/clubs.codex-plugin.zip
    - "Cursor": https://www.theprivateclubs.com/mcp/clubs.cursor-plugin.zip

    Each body says the plugin includes the Clubs skill and the MCP connection. Follow with a link to `/mcp/connect` for each app's steps. Note: for any other app, add a remote MCP server with the address, Streamable HTTP and no sign-in (link `/mcp/connect#another-app`).
  - `## Confirm the connection`: the check prompt. "The `search_clubs` tool answers with clubs and a link to each profile" (setup.md).
  - `## Ask about clubs`: the first ask, plus one more live ask from section 9.6.
  - `## Look up one club`: a prompt such as `Show me the profile of Desert Forest Golf Club`. Explain that `get_club` returns course, amenities, waitlist status and the profile link, with no fees.
  - `## Continue with Clubs`: 4 bullets linking `/mcp/connect`, `/mcp/tools`, `/use-cases`, `/api`.

### 4.2 Guides

Shared shape: intro paragraph, 4 to 6 task H2s, each with `<Tabs>` holding a "MCP" tab and an "API" tab where both apply. If only MCP applies, use a plain section with no tabs. The MCP tab shows a `text` prompt and names the tool and its arguments. The API tab shows a `bash` curl. Close with "Use the answers well" (the fees line plus the attribution line) and "Next" links. Length: 400 to 700 words each.

#### `guides/members.mdx`: Find and compare clubs (sidebar: For members)

- **description:** Search clubs by place and type, compare a shortlist and check membership status from your AI app
- `## Search by place and type`: MCP `search_clubs` with `state`, `type` and `query`, returning up to 25 A to Z. API `type`, `state`, `country`, `q`.
- `## Find clubs with an open waitlist`: MCP `waitlist: true` (keeps `open` only). API `waitlist=open`. Say plainly that an empty list means no club in that search is listed as open. Today `state: Arizona, type: golf, waitlist: true` returns an empty list.
- `## Compare two or three clubs`: `compare_clubs` with two or three slugs. MCP only, so no tabs.
- `## Check membership status`: `get_membership_info`, with what each waitlist value means (link `/reference/conventions#waitlist-values`).
- `## See fees and enquire`: fees are on the club's profile for account holders. A free account shows a price range (live `/llms.txt`). Members search, save, compare and enquire with clubs for free (live `/pricing` FAQ).
- Next: `/use-cases/members`, `/mcp/tools`.

#### `guides/clubs.mdx`: Check your club's listing (sidebar: For clubs)

- **description:** See your club the way AI apps see it, compare it with clubs nearby and claim the listing
- `## Find your club's slug`: `search_clubs` with `query` set to the club name. The slug is in the result and is the last part of the profile `url`.
- `## Read your profile`: `get_club`, with the fields it returns and no fees.
- `## See the clubs listed in your state`: MCP `get_clubs_by_state` (US states and Canadian provinces, up to 100, A to Z). API `state=` with paging for the full list.
- `## Compare with clubs nearby`: `compare_clubs`.
- `## Claim your listing`: free at https://www.theprivateclubs.com/list, with a work email and no credit card. Promise nothing about what happens after the claim.
- `## How AI apps find your club`: the MCP server, the API and the discovery files. Link `/reference/discovery-files`.
- Next: `/use-cases/clubs`.

#### `guides/brands-and-creators.mdx`: Build a list of clubs (sidebar: For brands and creators)

- **description:** Pull clubs by type and region for research, with the API for longer lists
- `## Start in your AI app`: `search_clubs` with `type` and `state` (25 maximum) and `get_clubs_by_state` (100 maximum).
- `## Pull a full list with the API`: `type` plus `state` or `country`, paged with `limit=200`. Show a bash loop.
- `## Filter by amenities`: MCP `amenities` takes stored codes, and a club must have all of them. Get real codes from a live result first; do not guess codes. The API `amenity` filter is not documented in v1 (see 9.3).
- `## Use the list well`: the fees line, the attribution line, and the data license line, copied exactly: "Under the data license, non-commercial use is free with attribution. Commercial use, bulk data access and API integrations beyond the public endpoints need a commercial license, and the license page gives the contact."
- Next: `/use-cases/brands-and-creators`, `/reference/club-types`.

#### `guides/real-estate-agents.mdx`: Look up the club behind a listing (sidebar: For real estate agents)

- **description:** Find the clubs in a town, check membership status and compare courses for a buyer
- `## Find the clubs in a town`: `search_clubs` with `query` set to the city and `state` (live: `query: Naples, state: Florida` returns rows). API `q` matches names only, so for a town use MCP or `state` plus paging.
- `## Check membership status`: `get_membership_info`.
- `## Compare the golf courses`: `compare_clubs`, covering holes, par, yardage, course architect and founded year.
- `## Share the profile link`: use the `url` from MCP results. Fees stay on the profile.
- `## Use the answers well`: fees and attribution lines, plus the data license.
- Next: `/use-cases/real-estate-agents`, `/use-cases/home-buyers`.

### 4.3 Use cases

#### `use-cases.mdx`: Use cases (sidebar: Overview, `mode: "wide"`)

- **description:** Questions people bring from their AI app, by audience, with a prompt you can adapt
- **Length:** 120 to 200 words. **Components:** `CardGroup cols={2}` (no images) with 5 Cards: Members (icon `users`), Clubs (`building`), Brands and creators (`sparkles`), Real estate agents (`briefcase`), Home buyers (`home`). Each body is one sentence; each href goes to its page.
- One intro sentence. Every example uses the free Clubs MCP and needs no account.

#### Use case page template (all five pages)

- **Length:** 300 to 450 words each.
- **Sections, in order:**
  1. Intro paragraph: who this is for and what they can ask.
  2. `## What you can ask`: the 3 live asks for the audience, verbatim from `MCP_USE_CASES` (section 9.6), as a short list with the tool in code font.
  3. `## Run it with your agent`: a horizontal `Card` titled "Connect Clubs to your agent" (icon `plug`, href `/mcp/connect`), then "Adapt this starting prompt:" and a `text` prompt with `[placeholders]` that runs all three asks in order. The prompt tells the app to include profile links and to say that fees are on the profile.
  4. `## How it works`: `Steps` with one `Step` per ask. Each says which tool fits, the arguments (use the verified ones in 9.6) and what comes back. Add a trimmed live result for at most one ask. Do not claim an app made a particular call; say which tool fits.
  5. `## What each system did`: 2 or 3 bullets. Clubs returned listed clubs and profile links. Your AI app chose the tool and wrote the answer.
  6. `## Before you share`: bullets. Fees are not included, so point people to the profile. Waitlist status can change, so check the profile. Then the attribution line and the data license.
  7. Next: the matching guide.

| File | Title | sidebarTitle | description |
|---|---|---|---|
| `use-cases/members.mdx` | Shortlist clubs to join | Members | Search clubs, compare a shortlist and check whether a waitlist is open, from your AI app |
| `use-cases/clubs.mdx` | See how your club reads in AI apps | Clubs | Read your profile as AI apps see it and compare it with clubs in your state |
| `use-cases/brands-and-creators.mdx` | Map clubs by type and region | Brands and creators | Find the clubs of one type in a state before you plan outreach or research |
| `use-cases/real-estate-agents.mdx` | Answer club questions for buyers | Real estate agents | Name the clubs in a town, check membership status and compare golf courses for a buyer |
| `use-cases/home-buyers.mdx` | Check the club before you buy a home | Home buyers | Find the golf clubs in a town, check a club's waitlist and compare two clubs side by side |

### 4.4 MCP

#### `mcp/connect.mdx`: Install Clubs

- **description:** Add the Clubs MCP server to Claude, ChatGPT, Claude Code, Codex, Cursor, Grok, Perplexity, OpenClaw, Hermes or any other AI app
- **Length:** 900 to 1,400 words. **Components:** `Tabs` (9 `Tab`s) holding `Steps`, plus `bash`, `json`, `yaml` and `text` blocks.
- **Sources:** live https://www.theprivateclubs.com/mcp/setup.md (200 today) and `src/lib/mcp/setup.ts` (`PARTS`, `CLIENTS`, anonymous flag). Copy UI labels exactly.
- **Outline:**
  - Intro: the address, key `clubs`, Streamable HTTP, the free line. Link the setup page for screenshots.
  - `## Use the setup line`: the setup line in a `text` block. Agent apps run the steps after you agree; chat apps tell you the clicks.
  - `## Choose your app`: `<Tabs>` in this order. Inside each Tab, add a `Steps` block and one line with the requirement (where given) and the older-entry note.
    - **Claude.** Requirement: "Works on every Claude plan, Free holds one custom connector." On Team and Enterprise, an owner adds it in **Organization settings > Connectors**.
      - *With the plugin* (flow verified 2026-10-18): (1) download https://www.theprivateclubs.com/mcp/clubs.claude-plugin.zip; (2) open **Customize > Plugins**, choose **Add > Upload plugin** and upload the ZIP; (3) open the plugin's **Connectors** tab and select **Connect**; (4) select **Add**, and Claude detects that no sign-in is needed; (5) select **Connect** again; (6) in a chat, press **Use** on the clubs connector.
      - *As a custom connector:* (1) copy the address; (2) open **Customize > Connectors**, select **+**, then **Add custom connector**; name it Clubs, paste the address and select **Continue**; (3) choose **No sign in**, select **Add**, then ask Claude about a club.
      - Older entry: replace it with Clubs.
    - **ChatGPT.** (1) copy the address; (2) go to chatgpt.com/plugins, select **+**, then **Add custom MCP server**; (3) name it Clubs, paste the address, tick **I understand and want to continue**, then select **Create as a plugin**; (4) start asking. No plan requirement line (UNCONFIRMED). Older entry: replace it with Clubs.
    - **Claude Code.** Requirement: user scope adds it to every project. (1) `claude mcp add --transport http clubs --scope user https://www.theprivateclubs.com/api/mcp`; (2) start Claude Code; `/mcp` shows clubs connected. Older entry: `claude mcp remove private-clubs` first. Also say that the Claude plugin syncs to Claude Code on the same account (site copy).
    - **Codex.** Requirement: the Codex CLI, IDE extension and ChatGPT desktop app share this setup. (1) `codex mcp add clubs --url https://www.theprivateclubs.com/api/mcp`; (2) start Codex and ask about a club. Older entry: `codex mcp remove private-clubs` first. *In the ChatGPT desktop app:* open **Settings > MCP servers**, then **Add server**; name it `clubs`, choose Streamable HTTP, paste the address, then **Save** and restart. *With the plugin:* download `clubs.codex-plugin.zip`, add the downloaded plugin in Codex, then start a new session.
    - **Cursor.** Requirement: needs the Cursor app; the link opens it. (1) [Add to Cursor](cursor://anysphere.cursor-deeplink/mcp/install?name=clubs&config=eyJ1cmwiOiJodHRwczovL3d3dy50aGVwcml2YXRlY2x1YnMuY29tL2FwaS9tY3AifQ%3D%3D); (2) open a chat in Cursor and ask about a club. *By hand:* add the `clubs` entry inside `mcpServers` in `~/.cursor/mcp.json`, beside any servers already there (JSON from setup.md). *With the plugin:* extract `clubs.cursor-plugin.zip` to `~/.cursor/plugins/local/clubs`, reload Cursor and confirm Clubs under **Customize**. Older entry: replace it with `clubs`. Leave out the 40-tools note (UNCONFIRMED). Check in preview that the `cursor://` link renders; if not, show it in a `text` block.
    - **Grok.** Requirement: every Grok user can add it, and Business and Enterprise admins add it for their team first. (1) copy the address; (2) go to grok.com/connectors, select **New Connector**, then **Custom**; (3) paste the address; (4) ask Grok about a club. Older entry: replace it with Clubs.
    - **Perplexity.** Requirement: on Enterprise, an admin turns on member connectors first. (1) copy the address; (2) open **Plugins** in the sidebar, select **Add custom**, **Connector**, then **Add MCP connector**; (3) choose **Remote**, name it Clubs, paste the address, pick Streamable HTTP and set Authentication to **None**, tick the box, then select **Add** and the card to turn it on. Note: no Plugins in your sidebar? Use **Customize > Connectors**. Older entry: replace it with Clubs.
    - **OpenClaw.** Requirement: needs OpenClaw, or use **Control UI > Settings > MCP > Add server**. (1) `openclaw mcp add clubs --url https://www.theprivateclubs.com/api/mcp --transport streamable-http`; (2) `openclaw mcp doctor clubs --probe`. Older entry: `openclaw mcp remove private-clubs` first.
    - **Hermes.** Requirement: needs Hermes Agent; the server goes in `~/.hermes/config.yaml`. (1) add the `mcp_servers: clubs: url: "https://www.theprivateclubs.com/api/mcp"` YAML; (2) run `/reload-mcp` in a running Hermes session. Older entry: replace it with `clubs`.
  - `## Another app`: add a remote MCP server in the app's settings, with name Clubs, key `clubs`, the address, transport Streamable HTTP and no authentication.
  - `## Replace an older entry`: the older-entry line, plus the three remove commands.
  - `## Check it worked`: start a new session if the app needs one, then send the check prompt.
  - `## Call the server without an app`: one `bash` block (POST JSON-RPC `tools/list` with `content-type: application/json` and `accept: application/json, text/event-stream`). It is stateless, so no session or initialize step is needed for a tools call; this was verified live. GET returns 405. Link `/mcp/tools`.
- **Leave out:** Windsurf, GitHub Copilot, n8n, Notion AI, Claude Desktop JSON, any sign-in or Authorize step, "about 40 tools", and the ChatGPT plan line.

#### `mcp/tools.mdx`: Available tools

- **description:** The five tools on the Clubs MCP server, with every parameter and returned field
- **Length:** 700 to 1,100 words. **Components:** markdown tables, plus small `json` argument examples.
- **Sources:** `src/app/api/mcp/route.ts` and live `tools/list`.
- **Outline:**
  - Intro: anyone can call every tool without an account. The tools only read the directory. Each result is one text item holding JSON. Your app can call `tools/list` for the full input schemas. Fees line.
  - One H2 per tool. Each holds a one-line purpose, a parameter table (Parameter, Type, Required, Description), a "Returns" sentence listing fields, limits and order, errors, and one example argument object.
    - `## Search clubs`: `search_clubs` (title "Search Clubs"). Parameters: `query` (string, no; case-insensitive part match on name, type or city), `state` (string, no; US state or Canadian province, full name recommended, and setting it keeps results in the US and Canada), `type` (string, no; case-insensitive exact match on the club's stored type, no grouping), `amenities` (array of strings, no; stored amenity codes, and the club must have all of them), `waitlist` (boolean, no; `true` keeps clubs whose `waitlist_status` is `open`, `false` does not filter). Nothing is required. With no `state`, the search is worldwide. It returns up to 25 clubs, A to Z by name, with no paging. Fields: `id, name, slug, city, state, country, type, waitlist_status, holes, par, yardage, course_architect, amenities, url`. Example: `{"state": "Arizona", "type": "golf"}`.
    - `## Get club details`: `get_club`. Parameter: `slug` (string, yes). Fields: `id, name, slug, city, state, country, type, description, waitlist_status, holes, par, yardage, course_architect, founded_year, total_members, amenities, website, phone, hero_image_url, url, membership_fees`. Not found returns an error result with `Club not found: <slug>`. Example: `{"slug": "desert-forest-golf-club"}`.
    - `## Get clubs by state`: `get_clubs_by_state`. Parameters: `state` (string, yes; US state or Canadian province), `type` (string, no; exact match). US and Canada only. Up to 100 clubs, A to Z. Fields: `id, name, slug, city, state, country, type, waitlist_status, holes, par, yardage, url`. Example: `{"state": "Florida", "type": "yacht"}`.
    - `## Compare clubs`: `compare_clubs`. Parameter: `slugs` (array of strings, yes, 2 or 3). It works for clubs in any country. Fields: `name, slug, location` (a "City, State" string)`, url, waitlist_status, holes, par, yardage, course_architect, founded_year, amenities, website`. Unknown slugs are left out without an error, and the order is not fixed. Example: `{"slugs": ["casa-cipriani-new-york", "spring-place-new-york"]}`.
    - `## Get membership info`: `get_membership_info`. Parameter: `slug` (string, yes). Fields: `name, slug, type, waitlist_status, url, membership_fees`. `membership_fees` is the tool fee message, followed by `See <url>` when the club has a profile. Not found returns `Club not found: <slug>`. Example: `{"slug": "the-estancia-club"}`.
  - `## How the tools behave` with H3s:
    - `### Which clubs appear`: listed public clubs only.
    - `### Profile links`: `url` is `https://www.theprivateclubs.com/clubs/{region}/{slug}`, where the region is the state or province for the US and Canada and the country elsewhere. It is `null` when a club has no region.
    - `### Place names`: use full names. Two-letter codes such as `FL` also resolve, but the tool descriptions ask for full names.
    - `### Types`: exact match. The `type` description lists 13 types. Link `/reference/club-types`.
    - `### Amenities`: stored snake_case codes, not labels.
    - `### Waitlist values`: `open`, `waitlist`, `by_invitation`, `invite_only`, `unknown`.
    - `### Fees`: no fees ever, and the exact message text.
    - `### Errors`: tool errors come back as error results: `Club not found: <slug>` and `Club search failed. Try again.` Request errors are covered in `/mcp/troubleshooting`.

#### `mcp/troubleshooting.mdx`: Troubleshooting

- **description:** Fix common problems with the Clubs MCP server, from missing tools to empty results
- **Length:** 700 to 1,000 words. No components except code blocks. Every H2 is a symptom, followed by 1 to 4 short paragraphs or bullets with the fix.
- **H2s, in order:**
  - `## Clubs tools do not appear`: start a new session. Check the key is `clubs`. In Claude, press **Use** on the connector. In Perplexity, select the card to turn it on. In Hermes, run `/reload-mcp`. In OpenClaw, run `openclaw mcp doctor clubs --probe`.
  - `## You see two Clubs entries`: older entry; remove `private-clubs`.
  - `## The address shows an error in a browser`: GET returns 405 with `Method not allowed, send MCP requests as POST`. This is expected. Add the address in your app instead.
  - `## A club is not found`: `Club not found: <slug>`. Get the slug from `search_clubs`. Only listed public clubs appear.
  - `## A comparison returns fewer clubs`: unknown slugs are left out without an error. Send two or three slugs.
  - `## A search stops at 25 clubs`: `search_clubs` returns at most 25, with no paging. Narrow by `state` or `type`, use `get_clubs_by_state` (up to 100), or use the API with paging.
  - `## A state search returns nothing`: `state` and `get_clubs_by_state` cover US states and Canadian provinces only. Elsewhere, use `search_clubs` with a city in `query` and no `state`.
  - `## A type search misses clubs`: MCP `type` is an exact match on the stored type. Legacy types such as `country_club` stay separate. Link `/reference/club-types`.
  - `## An amenity filter returns nothing`: use stored codes from a live result. Every code must match.
  - `## The answer has no fees`: by design. Use the fees line.
  - `## The request is rejected`: exact messages from `src/lib/mcp/harness.ts`: `Request too large, the limit is 2 MB`; `Invalid request, send at most 100 messages in one batch`; `Invalid request, send initialize on its own`; `Unsupported Mcp-Protocol-Version, send one of ...`; `Parse error, the body is not valid JSON`; `Invalid request, the body is empty`.
  - `## The server returns 503`: the request ran past the deadline or failed inside. Wait for `Retry-After` (10 seconds) and retry. Each response carries an `X-Request-Id`.
  - `## Still stuck`: the help line. Include the `X-Request-Id`.

### 4.5 API reference tab: hand-written pages

#### `api/openapi.mdx`: API overview

- **description:** The Clubs API spec, base URL, errors and versioning
- **Length:** 250 to 400 words, 3 to 4 code blocks.
- `## Spec URL`: `https://www.theprivateclubs.com/openapi.json` in a `text` block. Base URL `https://www.theprivateclubs.com`. Then a `Note`: the spec differs from the live API today (default `limit`, missing `country`, `q` and `waitlist`, a 12-field `Club` schema, a short `waitlist_status` enum, a 429 the API never sends, a second server that doubles `/api/v1`), so follow these docs and the club object. Drop the Note once the section 5 fixes ship.
- `## Endpoint`: one supported endpoint, `GET /api/v1/clubs`. Link `/api/openapi` only until the `API endpoints` group is added (section 5), then link it at `/api-reference/clubs/search-clubs`. Any other `/api/v1` path returns 404 with code `not_found` (live body: detail `This /api/v1 path does not exist.`, hint `Use GET /api/v1/clubs or read https://www.theprivateclubs.com/openapi.json`).
- `## Call it from your server`: REST sends no CORS headers, so browser calls from another origin fail. The MCP server allows any origin.
- `## Errors`: an RFC 9457 `application/problem+json` field table (`type`, `title`, `status`, `detail`, `code`, `hint`, `instance`) and a code table: `invalid_page` 400, `invalid_limit` 400, `not_found` 404, `clubs_unavailable` 500. Do not list 429.
- `## Versioning`: verbatim from the spec `x-versioning.policy`: "Breaking changes ship under a new /api/vN path. Deprecated versions send Deprecation and Sunset headers for at least 90 days."
- `## What you can do with the spec`: held back, because a client built from today's spec would be wrong. Bring it back once the section 5 fixes ship: bullets (generate a typed client, import into Postman or Insomnia, give it to an agent), plus `curl -o clubs-openapi.json https://www.theprivateclubs.com/openapi.json`.

#### `api/authentication.mdx`: Authentication and fees

- **description:** No keys or accounts, and what fee data each caller sees
- **Length:** 250 to 400 words.
- `## No API keys`: neither the MCP server nor the API takes a key, an Authorization header or an account. Leave out sign-in, OAuth and keys entirely.
- `## Fee access`: `fee_access` is `locked` for every API call (no figures). The values `band` (a range) and `exact` (club-verified figures) apply only to people signed in on theprivateclubs.com in a browser. Do not explain plans or how to get `exact`.
- `## Fee fields`: `initiation_fee`, `annual_dues` and `monthly_dues` are `null` when locked. `fee_views` has `initiation`, `annual`, `monthly` and `firstYear`, each with a `kind` of `none` (no figure stored), `locked`, `band` or `exact`. `fee_tier` (`verified`, `estimate`, `none`) labels where a figure comes from; everyone receives it, and it is never a figure.
- `## Fees over MCP`: the fees line, plus the tool fee message.
- `## Caching`: `Cache-Control: private, no-store` on every response.

#### `api/usage-limits.mdx`: Pricing and limits

- **description:** What Clubs costs, the data license, page sizes and the request limits on each surface
- **Length:** 350 to 600 words. **Components:** 1 or 2 tables.
- `## Pricing`: calls to the MCP server and the API are free and need no account. Members use Clubs free. Business plans for clubs, brands and creators are on https://www.theprivateclubs.com/pricing; link there and quote no prices.
- `## Data license`: one or two sentences summarising https://www.theprivateclubs.com/data/license. Non-commercial use is free with attribution. For commercial use, bulk access or integrations beyond the public endpoints, follow the contact on that page. Link it; do not restate more.
- `## API page size`: `limit` defaults to 50, the maximum is 200 and larger values are clamped. `page` starts at 1. A non-integer value returns 400.
- `## Rate limit headers`: every REST response sends `RateLimit: limit=120, remaining=119, reset=60` and `RateLimit-Policy: 120;w=60`. Keep within 120 requests a minute. Retry with backoff on 500 and 503. No enforcement claim and no 429.
- `## MCP limits`: a table with body 2 MB, at most 100 messages per batch, `initialize` sent on its own, `search_clubs` 25 results, `get_clubs_by_state` 100, `compare_clubs` 2 or 3 slugs, and 503 with `Retry-After: 10` when a request runs past the deadline. No request rate limit.
- `## Need more`: the help line.

### 4.6 Data reference

#### `reference/club-object.mdx`: Club object

- **description:** Every field a club carries in API responses and MCP tool results
- **Length:** 500 to 900 words. **Components:** tables.
- `## API club`: a Field, Type, Notes table for `id, name, slug, city, state, country, type, status, verified, hero_image_url, initiation_fee, annual_dues, monthly_dues, fee_tier, waitlist_status, holes, par, yardage, course_architect, founded_year, opened_year, currency, amenities, description, google_rating, google_review_count, fee_views`. Add a row for the response wrapper (`clubs`, `total`, `fee_access`). Say there is no `url` field.
- `## MCP results`: a Tool, Fields table, with notes for `url`, `location` and `membership_fees`. Link `/mcp/tools`.
- `## Field notes` with H3s:
  - Place fields: full names, for example `United States`. `state` holds the state or province for the US and Canada and a region elsewhere (live: `Scotland` with country `United Kingdom`).
  - `type`: link `/reference/club-types`.
  - `status`: list only values you see live, for example `unclaimed`, `active`, `coming_soon`. Say these are examples, not an exhaustive list.
  - `waitlist_status`: the five values.
  - `amenities`: codes, can be `null`.
  - Course fields: often `null` for non-golf clubs.
  - `founded_year` and `opened_year`.
  - `google_rating` and `google_review_count`: API only, can be `null`.
  - `description`: editorial text, can be `null`.
  - `currency`.
- `## Empty values`: `null` means not known. Never fill it in.

#### `reference/club-types.mdx`: Club types

- **description:** The 15 club types, how the API groups them and where legacy types go
- **Length:** 300 to 500 words. **Components:** 2 tables.
- `## Current types`: a Type, Browse page, API `type=` also matches table, sourced from `src/lib/browse-type-scope.ts` and `src/middleware.ts`. The types are `golf` (+ `country_club`), `city` (+ `social`, `arts`, `cigar`, `dining`, `wine`), `yacht`, `athletic` (+ `tennis`, `wellness`), `beach`, `equestrian`, `ski`, `automotive`, `aviation`, `outdoor` (+ `hunting`, `fishing`, `shooting`, `adventure`), `business`, `workspace`, `wellness`, `dining` and `residential`. The browse page is `https://www.theprivateclubs.com/clubs/{type}`.
- `## How the API and MCP match types`: the API groups as in the table. The MCP `type` is an exact match on the stored type, so `golf` there does not include `country_club`. The MCP tool description lists 13 types.
- `## Legacy types`: a Legacy, Now table: `social`, `arts`, `cigar` to `city`; `wine` to `dining`; `tennis` to `athletic`; `hunting`, `fishing`, `shooting`, `adventure` to `outdoor`; `country_club` to `golf`. `/clubs/{legacy}` answers 308 to the new page (live: `/clubs/tennis` to `/clubs/athletic`), and `/clubs/type/country_club` answers 308 to `/clubs/golf`.
- `## Browse URLs`: `/clubs/{type}` is canonical. Do not teach `/clubs/type/{type}`.

#### `reference/conventions.mdx`: Conventions

- **description:** How Clubs writes places, types, waitlist values and links, and how to cite it
- **Length:** 300 to 500 words.
- `## Places`: states and countries are full names. Two-letter state codes resolve in both MCP and the API. In the API, a state with no country stays in the US and Canada.
- `## Types`: snake_case. Link `/reference/club-types`.
- `## Waitlist values`: a value, meaning table. `open` means the club is listed as open to new members. `waitlist` means it is listed with a waitlist. `by_invitation` and `invite_only` both mean by invitation. `unknown` means not known. Keep the meanings plain and add nothing more.
- `## Profile links`: the MCP `url` format. API rows carry no link. Do not teach building links by hand.
- `## Fees`: never state or estimate a club's fees (live `/llms.txt`).
- `## Missing values`: `null` means not known. Leave the field out of what you show, or point people to the club's profile. Never invent a value.
- `## Attribution`: the attribution line, plus "Data from theprivateclubs.com shows..." as a pattern. Link the license for the exact format.

#### `reference/discovery-files.mdx`: Discovery files

- **description:** The public files that tell AI apps and crawlers where Clubs lives
- **Length:** 250 to 400 words. **Components:** 1 table.
- `## Files`: a File, What it holds table:
  - `https://www.theprivateclubs.com/mcp.json`: server name, version, endpoint, transport, the five tools, server key `clubs`, auth none, setup page.
  - `/openapi.json`: the REST spec.
  - `/llms.txt`: site overview for AI tools, regenerated hourly.
  - `/mcp/setup.md`: setup steps an agent reads and follows for its own app.
  - Leave out `/ai/context.md`: it uses the old brand and names `/ai/clubs.json`, which returns no clubs, as the club data API.
  - `/robots.txt`: allows AI crawlers.
- `## For AI crawlers`: robots.txt allows every user agent. It names AI crawlers such as GPTBot, ClaudeBot, PerplexityBot, Google-Extended and OAI-SearchBot. Copy the names from the live file.
- `## Which file to use`: one line each.
- **Leave out:** `/llms-full.txt` (stale), `/ai/clubs.json` (empty), `/agents.json` and `/ai-plugin.json` (not checked).

---

## 5. API endpoints group: generation and blockers

docs.json group (held back, not in docs.json until the spec fixes below ship):

```json
{ "group": "API endpoints", "openapi": "https://www.theprivateclubs.com/openapi.json", "pages": ["GET /api/v1/clubs"] }
```

Mintlify fetches the live spec at build time. It renders a page with a "Try it" playground and a cURL sample, so nobody hand-writes endpoint MDX. Only `GET /api/v1/clubs` is listed, so `/ai/clubs.json`, `/ai/context.md`, `/llms.txt`, `POST /api/mcp` and `/api/clubs` get no pages.

**Blockers before the API reference tab is public.** The live spec (`src/lib/openapi-spec.json`, OpenAPI 3.0.0, v1.1.0) would print untrue statements on the generated page. Fixing it is an app change that needs Chris's go (separate PR in the app repo, not this one):

1. `GET /api/v1/clubs` `limit` default is 24 in the spec but 50 in code. `/api/clubs` says default 24, max 100; the code uses 50 and 200.
2. The description says "Rate limit: 120 requests per 60 seconds" and the spec lists a 429 response. Nothing enforces either. Reword them as advisory headers, or drop them.
3. `servers[1]` is `https://www.theprivateclubs.com/api/v1` while paths already start with `/api/v1`, so the playground can build `/api/v1/api/v1/clubs`. Drop the second server.
4. Add `tags: ["Clubs"]` and summary `Search clubs` (today it is "REST API v1: search clubs"). Add `x-mint: { href: "/api-reference/clubs/search-clubs" }` to pin the slug the docs link to.
5. Add the parameters the docs teach and the code accepts: `country`, `q`, `waitlist` (comma list).
6. The `Club` schema lists 12 fields; the response has 27. Add the missing ones, and add `unknown` and `invite_only` to the `waitlist_status` enum.
7. `/ai/clubs.json` is described as having "real fee data", but it returns zero clubs and no fees. Fix the route or remove it from the spec.
8. `info.title` is "theprivateclubs.com API" and `info.contact.email` is chris@theprivateclubs.com. Suggest "Clubs API" and support@theprivateclubs.com.

Fallback if Chris prefers not to touch the app spec: commit a corrected copy as `openapi/clubs-v1.json` in this repo and point the group at it. Do this only on his say.

---

## 6. docs.json changes (applied)

| Key | Before | After |
|---|---|---|
| `theme` | `willow` | `mint` (Rainbrand's theme) |
| `name` | The Private Clubs | `Clubs` |
| `description` | none | Documentation for the Clubs MCP and REST API from Private Clubs |
| `colors` | primary `#171717` | primary `#171717`, light `#E5E5E5` (dark-mode accent), dark `#171717` |
| `background` | none | light `#FFFFFF`, dark `#0A0A0A` |
| `logo` | `/logo/light.svg`, `/logo/dark.svg` | removed, so the header shows the name Clubs as text. The two files are still Mintlify's wordmark and stay in the repo until a Clubs wordmark replaces them (see open items) |
| `favicon` | DuckDuckGo URL | kept as is (see open items) |
| `icons` | none | `{ "library": "tabler" }` |
| `styling` | none | `{ "codeblocks": "dark" }` |
| `navbar.links` | Support to mailto:data@theprivateclubs.com | Support to `mailto:support@theprivateclubs.com` |
| `navbar.primary` | "Visit Platform" to theprivateclubs.com | "Open Clubs" to `https://www.theprivateclubs.com/mcp` |
| `contextual` | none | options `copy, view, chatgpt, claude`, display `header`. `mcp` and `cursor` are left out because both install Mintlify's docs MCP server, which readers would confuse with the Clubs MCP |
| `navigation` | 2 tabs, 29 pages | the IA in section 3 |
| `footer.socials` | x | x, instagram, linkedin, youtube, website (from `src/components/home/atlas/homepage-footer.tsx`; TikTok is left out because it is not a known Mintlify social key) |
| `errors.404` | none | "This page does not exist or has moved" / "Check the link, or search the docs" |

Not set on purpose: `seo.metatags.canonical` (add `https://docs.theprivateclubs.com` once that domain is live; no DNS today), `banner`, `fonts`, `redirects` (the old docs were never live on that domain).

---

## 7. Retired and merged pages

Deleted with `git rm` (27 files):

| Old page | Fate |
|---|---|
| `api/overview.mdx` | merged into `api/openapi.mdx` and `api/index.mdx` |
| `api/rate-limits.mdx` | merged into `api/usage-limits.mdx` |
| `api/search-clubs.mdx` | replaced by the generated `GET /api/v1/clubs` page and `api/index.mdx` |
| `api/mcp-overview.mdx` | merged into `mcp/getting-started.mdx` and `introduction.mdx` |
| `api/mcp-tools.mdx` | replaced by `mcp/tools.mdx` |
| `api/mcp-connect.mdx` | replaced by `mcp/connect.mdx` |
| `api/data-model.mdx` | replaced by `reference/club-object.mdx` |
| `api/club-types-reference.mdx` | replaced by `reference/club-types.mdx` |
| `api/conventions.mdx` | replaced by `reference/conventions.mdx` |
| `api/get-club.mdx` | folded into `mcp/tools.mdx` and `reference/club-object.mdx` |
| `api/fee-data.mdx` | deleted (`get_fee_data` does not exist, and its fee figures break the fee policy) |
| `api/statistics.mdx` | deleted (`get_statistics` does not exist, and its counts are stale) |
| `platform/how-it-works.mdx`, `platform/club-types.mdx` | deleted (consumer content; the correct type list moves to `reference/club-types.mdx`) |
| `members/searching-clubs.mdx`, `comparing-clubs.mdx`, `applying-for-membership.mdx`, `concierge-service.mdx`, `member-community.mdx` | deleted (consumer how-tos, retired Concierge, plans that do not exist) |
| `clubs/claiming-your-listing.mdx`, `verified-profile.mdx`, `lead-management.mdx`, `analytics-dashboard.mdx`, `director-community.mdx` | deleted (the $500 and $99 plans, and features not built) |
| `clubs/ai-discovery.mdx` | deleted; its true lines go to `reference/discovery-files.mdx` |
| `pricing/members-plans.mdx`, `pricing/clubs-plans.mdx` | deleted; replaced by the Pricing section of `api/usage-limits.mdx`, which links /pricing |

Kept and rewritten in place: `introduction.mdx`, `api/authentication.mdx`.

---

## 8. Writing groups (disjoint)

Each group writes only its own files, reads this IA, and checks facts against the app source or live curl. Nobody edits `docs.json` or `IA.md`. Anyone who finds a fact here that is wrong reports it instead of working around it.

| Group | Files |
|---|---|
| 1. Getting started and guides | `introduction.mdx`, `how-clubs-works.mdx`, `guides/members.mdx`, `guides/clubs.mdx`, `guides/brands-and-creators.mdx`, `guides/real-estate-agents.mdx`, `AGENTS.md`, `README.md` |
| 2. MCP | `mcp/getting-started.mdx`, `mcp/connect.mdx`, `mcp/tools.mdx`, `mcp/troubleshooting.mdx` |
| 3. REST API and data reference | `api/index.mdx`, `api/openapi.mdx`, `api/authentication.mdx`, `api/usage-limits.mdx`, `reference/club-object.mdx`, `reference/club-types.mdx`, `reference/conventions.mdx`, `reference/discovery-files.mdx` |
| 4. Use cases | `use-cases.mdx`, `use-cases/members.mdx`, `use-cases/clubs.mdx`, `use-cases/brands-and-creators.mdx`, `use-cases/real-estate-agents.mdx`, `use-cases/home-buyers.mdx` |

Group 1 also rewrites the support files:

- `AGENTS.md`: replace the Mintlify starter. Remove the `npx skills add` line and the "prompt the user" line. Add the naming rules, house style, standard lines, the truth rules and the content boundaries from section 2 of this IA.
- `README.md`: a short description of the Clubs docs and how to preview with the Mintlify CLI. Remove the `npx skills add` block and the starter-kit copy.

---

## 9. Fact sheet (checked 2026-10-18)

### 9.1 MCP server

- Address `https://www.theprivateclubs.com/api/mcp`. serverInfo name and title `Clubs`, version `1.1.0`. Tools only (no resources or prompts). Stateless Streamable HTTP, POST only, JSON responses. GET and DELETE return 405 `Method not allowed, send MCP requests as POST` with `Allow: POST, OPTIONS`. CORS `Access-Control-Allow-Origin: *`. Every response carries `X-Request-Id`. No auth, account or key. (`src/app/api/mcp/route.ts`, `src/lib/mcp/harness.ts`, live)
- Limits: body 2 MB; 100 messages per batch; `initialize` sent alone; unsupported `Mcp-Protocol-Version` returns 400; deadline 50 s, then 503 with `Retry-After: 10`. No request rate limit. (`harness.ts`)
- `/mcp.json`: name Clubs, worldwide, version 1.1.0, five tools, `servers: [{key: clubs, url, transport: streamable-http, auth: none}]`, setup `https://www.theprivateclubs.com/mcp`. (live)
- Signed-in server: `POST /mcp` returns 503 `mcp_disabled`. Do not document it. (live)

### 9.2 Setup

- Nine apps, in this order: Claude, ChatGPT, Claude Code, Codex, Cursor, Grok, Perplexity, OpenClaw, Hermes (`MCP_APP_IDS`). Agent apps: Claude Code, Codex, Cursor, OpenClaw, Hermes. Chat apps: Claude, ChatGPT, Grok, Perplexity.
- Plugin ZIPs (live, 200): `/mcp/clubs.claude-plugin.zip`, `/mcp/clubs.codex-plugin.zip`, `/mcp/clubs.cursor-plugin.zip`. Each holds the MCP connection and a Clubs skill. There is no Claude Code marketplace (`MCP_MARKETPLACE` is null).
- Verification: only the Claude plugin flow was tested end to end (2026-10-18). The Codex and Cursor plugin steps come from vendor docs and mirror `/mcp`; test them before publishing.

### 9.3 REST API

- `GET https://www.theprivateclubs.com/api/v1/clubs` re-exports `GET /api/clubs`. No auth, no CORS. Response `{clubs, total, fee_access}`. Headers: `Cache-Control: private, no-store`, `X-Total-Clubs`, `X-Data-Source`, `RateLimit`, `RateLimit-Policy`. (`src/app/api/clubs/route.ts`, `src/lib/api-problem.ts`, live)
- Documented parameters: `type` (grouped), `state` (full name or two-letter code), `country` (name or ISO code), `q` (part of the name), `waitlist` (comma list; `by_invitation` also matches `invite_only`), `page` (from 1), `limit` (default 50, max 200, clamped). Verified live: `country=GB` and `country=United Kingdom` match the same clubs, and `state=FL` matches the same as `state=Florida`.
- Exist in code but are not documented in v1 (unstable, or not in the spec): `city`, `amenity`, `architect`, `holes`, `sort`, `coming`, `ranked`, `tour`, `operator`, `homes`, `access`, `membership`, `perPage`, `feeMin`, `feeMax`.
- Errors: `invalid_page` 400, `invalid_limit` 400, `clubs_unavailable` 500, `not_found` 404 (any other `/api/v1` path). Problem `type` URLs are `https://www.theprivateclubs.com/problems/{code}`.

### 9.4 Fees

- MCP: never. REST: `fee_access` is `locked` for every API call (there are no API keys, only the site session). `band` and `exact` are for people signed in on the site. (`src/lib/fees/access.ts`, `src/lib/fees/api.ts`)
- Live `/llms.txt`: "Do not state or estimate a club's fees". A free account shows a price range on the site.

### 9.5 Pricing and license

- Members are free (`/pricing` FAQ). The MCP and API are free for public club data (`/mcp` FAQ). Business plans Plus, Pro, Elite and Max are listed on `/pricing`, and every plan button reads "Not available yet" today, so link `/pricing` and quote no prices.
- Data license (`/data/license`): non-commercial use is free with attribution, AI training is allowed with citation, and commercial use, bulk access or integrations beyond the public endpoints need contact.
- Claiming a club is free, with a work email and no credit card (`/list`).

### 9.6 Live use cases with verified arguments

Results were checked with `tools/call` on 2026-10-18. Writers re-run them before publishing.

| Audience | Ask (verbatim) | Tool | Arguments | Result today |
|---|---|---|---|---|
| Members | Which private golf clubs in Arizona have an open waitlist? | `search_clubs` | `{"state":"Arizona","type":"golf","waitlist":true}` | empty list (no Arizona golf club listed as open) |
| Members | Compare the two clubs I'm choosing between | `compare_clubs` | `{"slugs":["casa-cipriani-new-york","spring-place-new-york"]}` | 2 clubs |
| Members | Is the waitlist open at this club? | `get_membership_info` | `{"slug":"the-estancia-club"}` | found |
| Clubs | How does our club's profile read on Clubs? | `get_club` | `{"slug":"desert-forest-golf-club"}` | found |
| Clubs | Which yacht clubs are listed in our state? | `get_clubs_by_state` | `{"state":"Florida","type":"yacht"}` | rows |
| Clubs | Compare our club with two clubs nearby | `compare_clubs` | pick 3 real nearby slugs and verify them | |
| Brands and creators | Which equestrian clubs are listed in Kentucky? | `search_clubs` | `{"state":"Kentucky","type":"equestrian"}` | rows |
| Brands and creators | List the golf clubs in Texas | `get_clubs_by_state` | `{"state":"Texas","type":"golf"}` | hits the 100 cap |
| Brands and creators | Which ski clubs are there in Colorado? | `search_clubs` | `{"state":"Colorado","type":"ski"}` | rows |
| Real estate agents | Which private clubs are in Naples, Florida? | `search_clubs` | `{"query":"Naples","state":"Florida"}` | hits the 25 cap |
| Real estate agents | What is the membership status at this club? | `get_membership_info` | any verified slug | |
| Real estate agents | Compare the golf courses at these two clubs | `compare_clubs` | two verified golf slugs | |
| Home buyers | Which golf clubs are in Scottsdale, Arizona? | `search_clubs` | `{"query":"Scottsdale","state":"Arizona","type":"golf"}` | hits the 25 cap |
| Home buyers | Is the waitlist open at this home's club? | `get_membership_info` | any verified slug | |
| Home buyers | Compare the two clubs these homes sit in | `compare_clubs` | two verified slugs | |

---

## 10. Open items for Chris

1. **License versus "Free" chips:** `/mcp` labels every use case "Free", including ones for brands and real estate agents, but `/data/license` limits free use to non-commercial. The docs state both plainly. Decide whether the license or the page copy changes.
2. **OpenAPI fixes:** the eight items in section 5 block the API reference tab.
3. **Logo files:** `logo/light.svg` and `logo/dark.svg` are still Mintlify's wordmark (kept, as asked). docs.json no longer points at them, so the header shows Clubs as text until a Clubs wordmark replaces them. The real files are in the app: `public/brand/tpc-logo-light.svg` and `public/brand/tpc-logo-dark.svg` (Private Clubs wordmark), and `public/brand/tpc-icon.svg` for the favicon. The favicon still comes from a DuckDuckGo URL. Both need your go.
4. **Members use case:** the `/mcp` ask "Which private golf clubs in Arizona have an open waitlist?" returns an empty list today. Few clubs carry `open`.
5. **`get_clubs_by_state` description:** it says "in a specific US state" but also covers Canadian provinces (`src/app/api/mcp/route.ts`). The `search_clubs` type list leaves out `workspace` and `residential`.
6. **Stale public files outside this repo:**
   - `/llms-full.txt` (Supabase Edge Function) still lists invented tools, the old key, old limits and the ThePrivateClubs.com brand.
   - `/llms.txt` teaches `/clubs/type/[type-slug]`.
   - `/ai/clubs.json` returns zero clubs.
   - `X-Data-Source: ThePrivateClubs.com` and `/data/license` use the old brand, and the license names chris@ as the contact.
7. **Repo files:** `LICENSE` says Copyright Mintlify. `.atlas-analysis.json` (the stale autogen brief) is removed from the working tree and from `.mintignore`.
8. **Before publishing:** re-curl `/mcp/setup.md` and `/mcp.json`, test the Codex and Cursor plugin steps, and preview the `cursor://` link.

---

## 11. Instructions found in repo files (not followed)

- `AGENTS.md` says: install the Mintlify skill with `npx skills add https://mintlify.com/docs`, and "Prompt the user to customize this file". It also suggests the Mintlify MCP servers.
- `README.md` gives the same `npx skills add` command.

Neither was run or acted on. Group 1 removes both lines in its rewrite.
