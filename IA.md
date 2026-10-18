# Private Clubs docs: information architecture

Plan for docs.theprivateclubs.com, the docs for the Private Clubs MCP server and REST API. It mirrors the structure of docs.rainbrand.com. Written 2026-10-18 on branch `feat/clubs-docs`. Nothing here is published.

**Naming update, 2026-10-18.** Chris reversed the Clubs naming: "rename everything from Clubs to Private Clubs, and add our logo where possible". The product and the MCP are Private Clubs ("the Private Clubs MCP"), the server key is `private-clubs`, and an entry under `clubs` is the older one. Section 2 and the page specs below follow this. The docs also take the website's look: theme `luma`, our logo, Instrument Sans, our blue and `style.css` (section 6).

**After PR #679, 2026-10-18.** The app's rename and API fixes are merged (84e3d856) and live: serverInfo and `/mcp.json` say Private Clubs with key `private-clubs`, the plugin ZIPs are `private-clubs.*` (the `clubs.*` links answer 308), `search_clubs` reads a comma in `query`, the spec is 1.2.0 with every section 5 blocker fixed, and the discovery files carry the brand. Branch `docs/after-679` brings the pages, `docs.json` (the `API endpoints` group), this IA and `AGENTS.md` up to date. Checked against the merged code and live GET requests on 2026-10-18.

**Data feed update, 2026-10-18.** Chris: "we dont want people to click and download our entire database and directly.. for that we want to charge a monthly fee for the data feed". Looking up, searching and comparing clubs through the MCP server and the API stays free, including for work, with credit and a link. Bulk download, scraping, mirroring, caching or storing the directory or a substantial part of it, building or enriching a database or dataset, AI training data collected through the MCP server or the API, and resale need a paid monthly data feed license (crawling the public pages for search, AI answers and AI training stays as robots.txt allows) (email support@theprivateclubs.com; no price, plan name or SLA is set). The app changes are on `feat/legal-mcp` (PR #689: terms section 8, sections 9 and 10, `/data/license`) and `feat/api-bulk-cap` (3f773c0b: `limit` maximum 50, the first 500 results of a search, 403 `data_feed_required`, spec 1.3.0). Neither was live when branch `docs/data-feed` was written. Release order: deploy `feat/api-bulk-cap` first, then merge PR #689 and `docs/data-feed` in the same window. Do not let #689 go live on its own: terms section 8 allows use "as documented" and links these docs, and the docs on main still show loops that page through every golf club in Arizona and in Texas, a line on pulling a whole country, and an API tab that pulls a whole state. Before `feat/api-bulk-cap` ships, `GET /api/clubs` (the site's own path: public, listed in the spec and named in `/llms.txt`) needs a depth cap too, because on 3f773c0b only `GET /api/v1/clubs` has the 500-result cap and a loop over `/api/clubs?page=N` never gets a 403. Chris picks the cap for the site's path. These docs document only `/api/v1/clubs` and never name `/api/clubs`. It removes the paging loops that pulled a whole state or type, adds a `Bulk data` section to `api/usage-limits` and `api/index`, and replaces the old license lines. Where this section and older lines below disagree, this section wins.

- **App source of truth (read only):** `/Users/mrcjk/Desktop/Projects/Project - theprivateclubs.com/repo-mcp-page` (main, at 84e3d856 or later)
- **Live checks:** `curl` against https://www.theprivateclubs.com (setup.md, mcp.json, openapi.json, llms.txt, /ai/context.md, /problems, /api/v1/clubs, /api/mcp). Every `/api/mcp` tool call writes a log row (`logMcpCall`), so the checks after #679 used GET requests only and read the MCP facts from the code.
- **Rainbrand reference copies:** `/private/tmp/claude-501/-Users-mrcjk-Desktop-Cursor-Projects-Project---theprivateclubs-com/436f968b-168e-466a-b181-7a497f24c2be/scratchpad/rb/`

Already applied: `docs.json` (section 6) and the removal of 27 retired pages with `git rm` (section 7). The four writing groups (section 8) write every new page.

---

## 1. Decisions in one place

1. **Two tabs, like Rainbrand:** Documentation (icon `book`) and API reference (icon `brackets-angle`). The tab label is sentence case, per house style.
2. **Only the free anonymous server is documented:** `https://www.theprivateclubs.com/api/mcp`. Sign in with Private Clubs (`/mcp`) is built but switched off: leave it out. Also leave out the 19 planned tools, fee bands over MCP, saves, enquiries, events, homes and network tools.
3. **One REST endpoint is documented:** `GET /api/v1/clubs`. Do not document `/ai/clubs.json` (returns zero clubs, still true live on 2026-10-18 after #679), `/api/clubs` (the unversioned path; spec 1.2.0 tells new integrations to use `/api/v1/clubs`), `/api/clubs/{slug}` (not in the spec, not under v1) or `/llms-full.txt` (stale).
4. **The endpoint page is generated** by Mintlify from the live spec at https://www.theprivateclubs.com/openapi.json, limited to `GET /api/v1/clubs`. The spec fixes (section 5) shipped in PR #679, so the `API endpoints` group is in docs.json (2026-10-18). The page is served at `/api-reference/clubs/search-clubs`, the path the spec's `x-mint` pins (checked with `mint dev`).
5. **Guides by audience and use cases by audience.** Guides are how-tos: steps, parameters and MCP or API tabs. Use cases follow Rainbrand's template: the asks, a prompt to adapt, how it works, and checks before you share. Both use only the 15 live asks in `MCP_USE_CASES` (`live: true`).
6. **Illustrations come from the site.** Three pages hold `Frame` slots for screenshots of the `/mcp` illustrations, in light and dark pairs under `images/`: the showcase window on `introduction.mdx`, the three setup steps on `mcp/connect.mdx` and the app picker on `mcp/getting-started.mdx`. Nobody draws new ones. Cards use Tabler icons on the site's blue tile.
7. **No hard-coded counts** of clubs, countries or connections. Live figures change hourly (`/mcp` and `/llms.txt` show them).
8. **Data license.** Calls cost nothing and need no account. Under https://www.theprivateclubs.com/data/license and section 8 of https://www.theprivateclubs.com/legal/terms (PR #689), looking up, searching and comparing clubs through the MCP server and the API is free, including for work, with credit and a link. Bulk use needs a paid monthly data feed license: email support@theprivateclubs.com. Every guide and use case links the license, and `api/usage-limits#bulk-data` lists the bulk uses.

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

- Everything is **Private Clubs** (Chris, 2026-10-18: "rename everything from Clubs to Private Clubs"): the company, the site, the MCP server and the API. Write "the Private Clubs MCP", "the Private Clubs MCP server" and "the Private Clubs API". The display name is Private Clubs and the server key is `private-clubs`.
- Never write "The Private Clubs", "ThePrivateClubs.com" or "TPC". Do not start a sentence with "The Private Clubs ...": restructure it. The domain stays theprivateclubs.com, and you can write "theprivateclubs.com" when you mean the site.
- Lowercase "clubs" stays the plain noun ("search clubs", "golf clubs"), and audience labels such as Clubs or Club teams stay as they are.
- The plugin ZIPs are `private-clubs.claude-plugin.zip`, `private-clubs.codex-plugin.zip` and `private-clubs.cursor-plugin.zip` (PR #679). The earlier `clubs.*` links answer 308 to them. Each plugin and its skill are named `private-clubs`.
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
- The `API endpoints` group is in docs.json (section 5). Link the generated endpoint page only from `api/openapi.mdx`, at `/api-reference/clubs/search-clubs`, the path the spec's `x-mint` pins. Everywhere else, link `/api/openapi`.

### Standard lines (copy exactly)

| Use | Text |
|---|---|
| Address | `https://www.theprivateclubs.com/api/mcp` |
| Transport | Streamable HTTP |
| Server key and name | key `private-clubs`, display name Private Clubs |
| Free line | Free, no account or API key needed |
| Setup line (paste into an AI app) | `Set up the Private Clubs MCP for me: read https://www.theprivateclubs.com/mcp/setup.md and follow the steps for this app` |
| Check prompt | `Search clubs in Florida` |
| First ask | `Find yacht clubs in Florida` |
| Older entry | Added `clubs` before? It is the same server under an earlier name. Replace it with `private-clubs`. In chat apps, as `src/lib/mcp/setup.ts` words it: Added Clubs before? Replace that older entry with Private Clubs. |
| Fees | Private Clubs never returns membership fees. Account holders see fees on each club's profile at theprivateclubs.com. |
| Fees (pages that cover the API too) | Private Clubs never returns membership fees over MCP, and API calls from your server or script return none. Account holders see fees on each club's profile at theprivateclubs.com. |
| Tool fee message (quote only as tool output) | `Membership fees are available to account holders at theprivateclubs.com. Members see exact verified fees.` |
| Attribution | Cite Private Clubs (theprivateclubs.com) and link the club's profile. See the [data license](https://www.theprivateclubs.com/data/license) for the attribution format and bulk use. |
| Help | Email support@theprivateclubs.com |
| Setup page | https://www.theprivateclubs.com/mcp |

---

## 3. Navigation

### Tab: Documentation (icon `book`)

| Group | Order | File | Title | sidebarTitle | Notes |
|---|---|---|---|---|---|
| Getting started | 1 | `introduction.mdx` | Introduction | | rewrite in place |
| | 2 | `how-clubs-works.mdx` | How Private Clubs works | | new |
| | 3 | `api/index.mdx` | API quickstart | | new, served at `/api` |
| | 4 | `mcp/getting-started.mdx` | MCP quickstart | | new |
| Guides | 1 | `guides/members.mdx` | Find and compare clubs | For members | new |
| | 2 | `guides/clubs.mdx` | Check your club's listing | For clubs | new |
| | 3 | `guides/brands-and-creators.mdx` | Find clubs by type and region | For brands and creators | new |
| | 4 | `guides/real-estate-agents.mdx` | Look up the club behind a listing | For real estate agents | new |
| Use cases | 1 | `use-cases.mdx` | Use cases | Overview | new, `mode: "wide"` |
| | 2 | `use-cases/members.mdx` | Shortlist clubs to join | Members | new |
| | 3 | `use-cases/clubs.mdx` | See how your club reads in AI apps | Club teams | new |
| | 4 | `use-cases/brands-and-creators.mdx` | Map clubs by type and region | Brands and creators | new |
| | 5 | `use-cases/real-estate-agents.mdx` | Answer club questions for buyers | Real estate agents | new |
| | 6 | `use-cases/home-buyers.mdx` | Check the club before you buy a home | Home buyers | new |
| MCP | 1 | `mcp/connect.mdx` | Install Private Clubs | | new |
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
| API endpoints | 1 | generated: `GET /api/v1/clubs` | Search clubs (the spec summary) | added 2026-10-18, after PR #679 shipped the section 5 fixes |

That makes 24 hand-written pages, plus the generated page.

---

## 4. Page specs

Word counts are prose words, excluding code. Facts cite the source a writer should check. Section 9 collects the shared facts.

### 4.1 Getting started

#### `introduction.mdx`: Introduction

- **description:** Search and compare private clubs from your AI app or your own product, free and without an account
- **Length:** 200 to 300 words. **Components:** `CardGroup cols={2}` with 2 `Card`s.
- **Outline:**
  - Opening, in two short paragraphs. Private Clubs has an MCP server and a REST API. Both serve the public directory at theprivateclubs.com: private membership clubs worldwide, with profile, course, amenities, waitlist status and a link to each club's profile. Use the free line. Then the fees line.
  - `## Start using Private Clubs`: a CardGroup with "Connect an AI app" (icon `plug`, href `/mcp/getting-started`, body: install Private Clubs in Claude, ChatGPT, Cursor or another AI app) and "Build with the API" (icon `brackets-angle`, href `/api`, body: call `GET /api/v1/clubs` from your server).
  - `## What a club record holds`: define **club record** in bold on first use as the fields Private Clubs returns for one listed club. Link `/reference/club-object` and `/use-cases`.
  - `## Get help`: the help line.
- **Leave out:** counts, "North America", Windsurf, the old Platform, Members, Clubs and Pricing content, and sign-in.

#### `how-clubs-works.mdx`: How Private Clubs works

- **description:** One clubs directory, served to AI apps through MCP and to your product through the REST API
- **Length:** 300 to 450 words. **Components:** one `mermaid` flowchart LR and one `Note`.
- **Outline:**
  - Opening paragraph: what flows where.
  - Mermaid: `Club listings on theprivateclubs.com` to `Private Clubs MCP server` and `Private Clubs REST API`, then to `AI apps and your product`, then to `Answers with profile links`.
  - `## Choose MCP or the API`: adapt the `/mcp` FAQ answer (`src/app/(marketing)/mcp/mcp-faq-content.ts`). MCP connects Private Clubs to compatible agents. The REST API lets you integrate Private Clubs into an application, backend or automated workflow. Both read the same directory. Name the 5 tools inline and the one endpoint.
  - `## What comes back`: listed public clubs only (drafts and quarantined rows never appear). Profile basics, course data (holes, par, yardage, architect), amenities and waitlist status. MCP results carry a `url` to the club's profile; REST rows do not include a profile link.
  - `## How fees work`: the fees line. API responses carry `fee_access`, which is `locked` for every API call. Link `/api/authentication`.
  - `## Keep a listing current`: a club can claim its listing free at https://www.theprivateclubs.com/list, with a work email and no credit card (live `/list`). Link `/guides/clubs`.
  - No `Note`: the one that split the names went with the 2026-10-18 rename.

#### `api/index.mdx`: API quickstart

- **description:** Make your first request to the Private Clubs API in five steps
- **Length:** 450 to 700 words, 5 to 7 code blocks. Use numbered H2s, as Rainbrand does, not `Steps`.
- **Outline:**
  - Intro line: no key, no account; call it from your server, because the API sends no CORS headers.
  - `## 1. Send your first request`: `curl "https://www.theprivateclubs.com/api/v1/clubs?type=golf&state=Arizona&limit=5"` (verified live, returns rows).
  - `## 2. Read the response`: body `{clubs, total, fee_access}`, with a trimmed live JSON sample of 1 club, no `description`. Point out `fee_access: "locked"`, null fee fields and `fee_views` kinds `locked` or `none`. Headers: `X-Total-Clubs`, `Cache-Control: private, no-store`. Link `/reference/club-object`.
  - `## 3. Narrow by place and type`: `type` uses grouped categories (link `/reference/club-types`). `state` takes a full name or a two-letter code (`FL` and `Florida` return the same clubs). `country` takes a name or ISO code (`United Kingdom` and `GB` match the same clubs). With no country the search is worldwide, but a state with no country stays in the US and Canada. `q` matches part of the club name. `waitlist` takes a comma-separated list of `open`, `waitlist` and `by_invitation`. Document only these filters (see 9.3).
  - `## 4. Page through results`: `page` starts at 1. `limit` defaults to 50, the maximum, and larger values are clamped to 50. Both are whole numbers from 1: `0` answers 400, and leading zeros are fine. `page` times `limit`, with `limit` clamped to 50, can be at most 500; past that, 403 `data_feed_required` whatever the `total`, even when `total` is below 500. Stop when `page * limit` reaches `total` or 500. A page past the last result, within the first 500, answers 200 with an empty `clubs` array and the real `total`. Show one `page=2` request, never a loop that pages through every result.
  - `## 5. Handle errors`: `application/problem+json` with `type`, `title`, `status`, `detail`, `code`, `hint`, `instance`. Show the live `invalid_limit` body from `curl ".../api/v1/clubs?limit=abc"`. Link `/api/openapi#errors`.
  - `## Next steps`: links to `/api/openapi`, `/api/authentication`, `/reference/club-object`, `/reference/club-types`.
- **Leave out:** `/ai/clubs.json`, `/api/clubs`, fee filters (`feeMin`, `feeMax`), rate-limit enforcement and browser calls.

#### `mcp/getting-started.mdx`: MCP quickstart

- **description:** Connect Private Clubs to your AI app and ask your first question
- **Length:** 300 to 450 words. **Components:** `CardGroup cols={3}` with 3 download `Card`s, one `Note`, and `text` prompt blocks.
- **Outline:**
  - Intro line: install Private Clubs, check it works, then ask about clubs. Use the free line.
  - `## Install Private Clubs`: start with the setup line in a `text` block. "Agent apps such as Claude Code, Codex and Cursor run the setup for you. Chat apps walk you through the clicks." Then a CardGroup of 3 download Cards (icon `download`):
    - "Claude and Claude Code": https://www.theprivateclubs.com/mcp/private-clubs.claude-plugin.zip
    - "Codex": https://www.theprivateclubs.com/mcp/private-clubs.codex-plugin.zip
    - "Cursor": https://www.theprivateclubs.com/mcp/private-clubs.cursor-plugin.zip

    Each body says the plugin includes the Private Clubs skill and the MCP connection. Follow with a link to `/mcp/connect` for each app's steps. Note: for any other app, add a remote MCP server with the address, Streamable HTTP and no sign-in (link `/mcp/connect#another-app`).
  - `## Confirm the connection`: the check prompt. "The `search_clubs` tool answers with clubs and a link to each profile" (setup.md).
  - `## Ask about clubs`: the first ask, plus one more live ask from section 9.6.
  - `## Look up one club`: a prompt such as `Show me the profile of Desert Forest Golf Club`. Explain that `get_club` returns course, amenities, waitlist status and the profile link, with no fees.
  - `## Continue with Private Clubs`: 4 bullets linking `/mcp/connect`, `/mcp/tools`, `/use-cases`, `/api`.

### 4.2 Guides

Shared shape: intro paragraph, 4 to 6 task H2s, each with `<Tabs>` holding a "MCP" tab and an "API" tab where both apply. If only MCP applies, use a plain section with no tabs. The MCP tab shows a `text` prompt and names the tool and its arguments. The API tab shows a `bash` curl. Close with "Use the answers well" (the fees line plus the attribution line) and "Next" links. Length: 400 to 700 words each.

#### `guides/members.mdx`: Find and compare clubs (sidebar: For members)

- **description:** Search clubs by place and type, compare a shortlist and check membership status from your AI app
- `## Search by place and type`: MCP `search_clubs` with `state`, `type` and `query`, returning up to 25, best known first (live `/mcp`, from main's ranked order, 2026-10-18). API `type`, `state`, `country`, `q`.
- `## Find clubs with an open waitlist`: MCP `waitlist: true` (keeps `open` only). API `waitlist=open`. Say plainly that an empty list means no club in that search is listed as open. Today `state: Arizona, type: golf, waitlist: true` returns an empty list.
- `## Compare two or three clubs`: `compare_clubs` with two or three slugs. MCP only, so no tabs.
- `## Check membership status`: `get_membership_info`, with what each waitlist value means (link `/reference/conventions#waitlist-values`).
- `## See fees and enquire`: fees are on the club's profile for account holders. A free account shows a price range (live `/llms.txt`). Members search, save, compare and enquire with clubs for free (live `/pricing` FAQ).
- Next: `/use-cases/members`, `/mcp/tools`.

#### `guides/clubs.mdx`: Check your club's listing (sidebar: For clubs)

- **description:** See your club the way AI apps see it, compare it with clubs nearby and claim the listing
- `## Find your club's slug`: `search_clubs` with `query` set to the club name. The slug is in the result and is the last part of the profile `url`.
- `## Read your profile`: `get_club`, with the fields it returns and no fees.
- `## See the clubs listed in your state`: MCP `get_clubs_by_state` (US states and Canadian provinces, up to 100, best known first). API `state=` with paging, up to the first 500 results of a search.
- `## Compare with clubs nearby`: `compare_clubs`.
- `## Claim your listing`: free at https://www.theprivateclubs.com/list, with a work email and no credit card. Promise nothing about what happens after the claim.
- `## How AI apps find your club`: the MCP server, the API and the discovery files. Link `/reference/discovery-files`.
- Next: `/use-cases/clubs`.

#### `guides/brands-and-creators.mdx`: Find clubs by type and region (sidebar: For brands and creators)

- **description:** Find the clubs of one type in a region for research, in your AI app or with the API
- `## Start in your AI app`: `search_clubs` with `type` and `state` (25 maximum) and `get_clubs_by_state` (100 maximum).
- `## Search further with the API`: `type` plus `state`, 50 a page, up to the first 500 results of a search. One request, no export loop, and a `Note` that collecting the directory or building a dataset is bulk use and needs the data feed license. Point readers to `q` or `waitlist` to narrow a search to the clubs they want, never to finish a list past the 500-result cap. Whether a brand's outreach list counts as free lookup or data feed use is Chris's call, so the guide frames lookups and does not say either way.
- `## Filter by amenities`: MCP `amenities` takes stored codes, and a club must have all of them. Get real codes from a live result first; do not guess codes. The API `amenity` filter is not documented in v1 (see 9.3).
- `## Use the results well`: the fees line, the attribution line, and the "Free use and the data feed" line from `AGENTS.md`, copied exactly.
- Next: `/use-cases/brands-and-creators`, `/reference/club-types`.

#### `guides/real-estate-agents.mdx`: Look up the club behind a listing (sidebar: For real estate agents)

- **description:** Find the clubs in a town, check membership status and compare courses for a buyer
- `## Find the clubs in a town`: `search_clubs` with `query` set to the city and `state` (live: `query: Naples, state: Florida` returns rows). API `q` matches names only, so for a town use MCP. No API tab: pulling a whole state to filter by city runs past the 500-result cap.
- `## Check membership status`: `get_membership_info`.
- `## Compare the golf courses`: `compare_clubs`, covering holes, par, yardage, course architect and founded year.
- `## Share the profile link`: use the `url` from MCP results. Fees stay on the profile.
- `## Use the answers well`: fees and attribution lines, plus the data license.
- Next: `/use-cases/real-estate-agents`, `/use-cases/home-buyers`.

### 4.3 Use cases

#### `use-cases.mdx`: Use cases (sidebar: Overview, `mode: "wide"`)

- **description:** Questions people bring from their AI app, by audience, with a prompt you can adapt
- **Length:** 120 to 200 words. **Components:** `CardGroup cols={2}` (no images) with 5 Cards: Members (icon `users`), Clubs (`building`), Brands and creators (`sparkles`), Real estate agents (`briefcase`), Home buyers (`home`). Each body is one sentence; each href goes to its page.
- One intro sentence. Every example uses the free Private Clubs MCP and needs no account.

#### Use case page template (all five pages)

- **Length:** 300 to 450 words each.
- **Sections, in order:**
  1. Intro paragraph: who this is for and what they can ask.
  2. `## What you can ask`: the 3 live asks for the audience, verbatim from `MCP_USE_CASES` (section 9.6), as a short list with the tool in code font.
  3. `## Run it with your agent`: a horizontal `Card` titled "Connect Private Clubs to your AI app" (icon `plug`, href `/mcp/connect`), then "Adapt this starting prompt:" and a `text` prompt with `[placeholders]` that runs all three asks in order. The prompt tells the app to include profile links and to say that fees are on the profile.
  4. `## How it works`: `Steps` with one `Step` per ask. Each says which tool fits, the arguments (use the verified ones in 9.6) and what comes back. Add a trimmed live result for at most one ask. Do not claim an app made a particular call; say which tool fits.
  5. `## What each system did`: 2 or 3 bullets. Private Clubs returned listed clubs and profile links. Your AI app chose the tool and wrote the answer.
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

#### `mcp/connect.mdx`: Install Private Clubs

- **description:** Add the Private Clubs MCP server to Claude, ChatGPT, Claude Code, Codex, Cursor, Grok, Perplexity, OpenClaw, Hermes or any other AI app
- **Length:** 900 to 1,400 words. **Components:** `Tabs` (9 `Tab`s) holding `Steps`, plus `bash`, `json`, `yaml` and `text` blocks.
- **Sources:** live https://www.theprivateclubs.com/mcp/setup.md (200 today) and `src/lib/mcp/setup.ts` (`PARTS`, `CLIENTS`, anonymous flag). Copy UI labels exactly.
- **Outline:**
  - Intro: the address, key `private-clubs`, Streamable HTTP, the free line. Link the setup page for screenshots.
  - `## Use the setup line`: the setup line in a `text` block. Agent apps run the steps after you agree; chat apps tell you the clicks.
  - `## Choose your app`: `<Tabs>` in this order. Inside each Tab, add a `Steps` block and one line with the requirement (where given) and the older-entry note.
    - **Claude.** Requirement: "Works on every Claude plan, Free holds one custom connector." On Team and Enterprise, an owner adds it in **Organization settings > Connectors**.
      - *With the plugin* (flow verified 2026-10-18): (1) download https://www.theprivateclubs.com/mcp/private-clubs.claude-plugin.zip; (2) open **Customize > Plugins**, choose **Add > Upload plugin** and upload the ZIP; (3) open the plugin's **Connectors** tab and select **Connect**; (4) select **Add**, and Claude detects that no sign-in is needed; (5) select **Connect** again; (6) in a chat, press **Use** on the plugin's connector.
      - *As a custom connector:* (1) copy the address; (2) open **Customize > Connectors**, select **+**, then **Add custom connector**; name it Private Clubs, paste the address and select **Continue**; (3) choose **No sign in**, select **Add**, then ask Claude about a club.
      - Older entry: replace it with Private Clubs.
    - **ChatGPT.** (1) copy the address; (2) go to chatgpt.com/plugins, select **+**, then **Add custom MCP server**; (3) name it Private Clubs, paste the address, tick **I understand and want to continue**, then select **Create as a plugin**; (4) start asking. No plan requirement line (UNCONFIRMED). Older entry: replace it with Private Clubs.
    - **Claude Code.** Requirement: user scope adds it to every project. (1) `claude mcp add --transport http private-clubs --scope user https://www.theprivateclubs.com/api/mcp`; (2) start Claude Code; `/mcp` shows private-clubs connected. Older entry: `claude mcp remove clubs` first. Also say that the Claude plugin syncs to Claude Code on the same account (site copy).
    - **Codex.** Requirement: the Codex CLI, IDE extension and ChatGPT desktop app share this setup. (1) `codex mcp add private-clubs --url https://www.theprivateclubs.com/api/mcp`; (2) start Codex and ask about a club. Older entry: `codex mcp remove clubs` first. *In the ChatGPT desktop app:* open **Settings > MCP servers**, then **Add server**; name it `private-clubs`, choose Streamable HTTP, paste the address, then **Save** and restart. *With the plugin:* download `private-clubs.codex-plugin.zip`, add the downloaded plugin in Codex, then start a new session.
    - **Cursor.** Requirement: needs the Cursor app; the link opens it. (1) [Add to Cursor](cursor://anysphere.cursor-deeplink/mcp/install?name=private-clubs&config=eyJ1cmwiOiJodHRwczovL3d3dy50aGVwcml2YXRlY2x1YnMuY29tL2FwaS9tY3AifQ%3D%3D); (2) open a chat in Cursor and ask about a club. *By hand:* add the `private-clubs` entry inside `mcpServers` in `~/.cursor/mcp.json`, beside any servers already there (JSON from setup.md). *With the plugin:* extract `private-clubs.cursor-plugin.zip` to `~/.cursor/plugins/local/private-clubs`, then "Reload Cursor and confirm Private Clubs under Customize" (setup.ts, literal). Older entry: replace `clubs` with `private-clubs`. Leave out the 40-tools note (UNCONFIRMED). Check in preview that the `cursor://` link renders; if not, show it in a `text` block.
    - **Grok.** Requirement: every Grok user can add it, and Business and Enterprise admins add it for their team first. (1) copy the address; (2) go to grok.com/connectors, select **New Connector**, then **Custom**; (3) paste the address; (4) ask Grok about a club. Older entry: replace it with Private Clubs.
    - **Perplexity.** Requirement: on Enterprise, an admin turns on member connectors first. (1) copy the address; (2) open **Plugins** in the sidebar, select **Add custom**, **Connector**, then **Add MCP connector**; (3) choose **Remote**, name it Private Clubs, paste the address, pick Streamable HTTP and set Authentication to **None**, tick the box, then select **Add** and the card to turn it on. Note: no Plugins in your sidebar? Use **Customize > Connectors**. Older entry: replace it with Private Clubs.
    - **OpenClaw.** Requirement: needs OpenClaw, or use **Control UI > Settings > MCP > Add server**. (1) `openclaw mcp add private-clubs --url https://www.theprivateclubs.com/api/mcp --transport streamable-http`; (2) `openclaw mcp doctor private-clubs --probe`. Older entry: `openclaw mcp remove clubs` first.
    - **Hermes.** Requirement: needs Hermes Agent; the server goes in `~/.hermes/config.yaml`. (1) add the `mcp_servers: private-clubs: url: "https://www.theprivateclubs.com/api/mcp"` YAML; (2) run `/reload-mcp` in a running Hermes session. Older entry: replace `clubs` with `private-clubs`.
  - `## Another app`: add a remote MCP server in the app's settings, with name Private Clubs, key `private-clubs`, the address, transport Streamable HTTP and no authentication.
  - `## Replace an older entry`: the older-entry line, plus the three remove commands.
  - `## Check it worked`: start a new session if the app needs one, then send the check prompt.
  - `## Call the server without an app`: one `bash` block (POST JSON-RPC `tools/list` with `content-type: application/json` and `accept: application/json, text/event-stream`). It is stateless, so no session or initialize step is needed for a tools call; this was verified live. GET returns 405. Link `/mcp/tools`.
- **Leave out:** Windsurf, GitHub Copilot, n8n, Notion AI, Claude Desktop JSON, any sign-in or Authorize step, "about 40 tools", and the ChatGPT plan line.

#### `mcp/tools.mdx`: Available tools

- **description:** The five tools on the Private Clubs MCP server, with every parameter and returned field
- **Length:** 700 to 1,100 words. **Components:** markdown tables, plus small `json` argument examples.
- **Sources:** `src/app/api/mcp/route.ts` and live `tools/list`.
- **Outline:**
  - Intro: anyone can call every tool without an account. The tools only read the directory. Each result is one text item holding JSON. Your app can call `tools/list` for the full input schemas. Fees line.
  - One H2 per tool. Each holds a one-line purpose, a parameter table (Parameter, Type, Required, Description), a "Returns" sentence listing fields, limits and order, errors, and one example argument object.
    - `## Search clubs`: `search_clubs` (title "Search Private Clubs", from `src/app/api/mcp/route.ts`, merged in #679). Parameters: `query` (string, no; case-insensitive part match on name, type or city; a comma adds a place, see Place names), `state` (string, no; US state or Canadian province, full name recommended, and setting it keeps results in the US and Canada), `type` (string, no; case-insensitive exact match on the club's stored type, no grouping), `amenities` (array of strings, no; stored amenity codes, and the club must have all of them), `waitlist` (boolean, no; `true` keeps clubs whose `waitlist_status` is `open`, `false` does not filter). Nothing is required. With no `state` and no place in `query`, the search is worldwide. It returns up to 25 clubs, best known first, with no paging. Fields: `id, name, slug, city, state, country, type, waitlist_status, holes, par, yardage, course_architect, amenities, url`. Example: `{"state": "Arizona", "type": "golf"}`.
    - `## Get club details`: `get_club`. Parameter: `slug` (string, yes). Fields: `id, name, slug, city, state, country, type, description, waitlist_status, holes, par, yardage, course_architect, founded_year, total_members, amenities, website, phone, hero_image_url, url, membership_fees`. Not found returns an error result with `Club not found: <slug>`. Example: `{"slug": "desert-forest-golf-club"}`.
    - `## Get clubs by state`: `get_clubs_by_state`. Parameters: `state` (string, yes; US state or Canadian province), `type` (string, no; exact match). US and Canada only. Up to 100 clubs, best known first. Fields: `id, name, slug, city, state, country, type, waitlist_status, holes, par, yardage, url`. Example: `{"state": "Florida", "type": "yacht"}`.
    - `## Compare clubs`: `compare_clubs`. Parameter: `slugs` (array of strings, yes, 2 or 3). It works for clubs in any country. Fields: `name, slug, location` (city and state, the country when there is no state, missing parts left out, `null` when all three are missing)`, url, waitlist_status, holes, par, yardage, course_architect, founded_year, amenities, website`. Unknown slugs are left out without an error, and the order is not fixed. Example: `{"slugs": ["casa-cipriani-new-york", "spring-place-new-york"]}`.
    - `## Get membership info`: `get_membership_info`. Parameter: `slug` (string, yes). Fields: `name, slug, type, waitlist_status, url, membership_fees`. `membership_fees` is the tool fee message, followed by `See <url>` when the club has a profile. Not found returns `Club not found: <slug>`. Example: `{"slug": "the-estancia-club"}`.
  - `## How the tools behave` with H3s:
    - `### Which clubs appear`: listed public clubs only. `search_clubs` and `get_clubs_by_state` rank best known first and show a club listed twice in one city and state once, unless its name is only generic words and the city's name (`src/lib/mcp/club-ranking.ts`).
    - `### Profile links`: `url` is `https://www.theprivateclubs.com/clubs/{region}/{slug}`, where the region is the state or province for the US and Canada and the country elsewhere. It is `null` when a club has no region.
    - `### Place names`: use full names. Two-letter codes such as `FL` also resolve, but the tool descriptions ask for full names. `search_clubs` reads a comma in `query` (`src/lib/mcp/club-search.ts`, `splitCityRegion`): only the first part is searched; a US state or Canadian province tail (full name in any case, or a two-letter code in capitals) filters by that state; else a known country tail filters by country; a state wins over a country with the same code or name (`Toronto, CA` is California, `Tbilisi, Georgia` the US state; `Tbilisi, GE` is the country); two-letter tails with any lowercase letter and tails that name no place are ignored; a passed `state` wins.
    - `### Types`: exact match. The `type` description lists 13 types. Link `/reference/club-types`.
    - `### Amenities`: stored snake_case codes, not labels.
    - `### Waitlist values`: `open`, `waitlist`, `by_invitation`, `invite_only`, `closed`, `unknown`, as spec 1.2.0 lists them.
    - `### Fees`: no fees ever, and the exact message text.
    - `### Errors`: tool errors come back as error results: `Club not found: <slug>` and `Club search failed. Try again.` Request errors are covered in `/mcp/troubleshooting`.

#### `mcp/troubleshooting.mdx`: Troubleshooting

- **description:** Fix common problems with the Private Clubs MCP server, from missing tools to empty results
- **Length:** 700 to 1,000 words. No components except code blocks. Every H2 is a symptom, followed by 1 to 4 short paragraphs or bullets with the fix.
- **H2s, in order:**
  - `## Private Clubs tools do not appear`: start a new session. Check the key is `private-clubs`. In Claude, press **Use** on the connector. In Perplexity, select the card to turn it on. In Hermes, run `/reload-mcp`. In OpenClaw, run `openclaw mcp doctor private-clubs --probe`.
  - `## You see two Private Clubs entries`: older entry; remove `clubs`.
  - `## The address shows an error in a browser`: GET returns 405 with `Method not allowed, send MCP requests as POST`. This is expected. Add the address in your app instead.
  - `## A club is not found`: `Club not found: <slug>`. Get the slug from `search_clubs`. Only listed public clubs appear.
  - `## A comparison returns fewer clubs`: unknown slugs are left out without an error. Send two or three slugs.
  - `## A search stops at 25 clubs`: `search_clubs` returns at most 25, with no paging. Narrow by `state` or `type`, use `get_clubs_by_state` (up to 100), or use the API with paging.
  - `## A state search returns nothing`: `state` and `get_clubs_by_state` cover US states and Canadian provinces only. Elsewhere, use `search_clubs` with no `state` and the city and country in `query`, such as `Sydney, Australia`. (No entry for a comma in `query`: PR #679 removed that failure, so the old "A search fails every time" entry is gone.)
  - `## A type search misses clubs`: MCP `type` is an exact match on the stored type. Legacy types such as `country_club` stay separate. Link `/reference/club-types`.
  - `## An amenity filter returns nothing`: use stored codes from a live result. Every code must match.
  - `## The answer has no fees`: by design. Use the fees line.
  - `## The request is rejected`: exact messages from `src/lib/mcp/harness.ts`: `Request too large, the limit is 2 MB`; `Invalid request, send at most 100 messages in one batch`; `Invalid request, send initialize on its own`; `Unsupported Mcp-Protocol-Version, send one of ...`; `Parse error, the body is not valid JSON`; `Invalid request, the body is empty`.
  - `## The server returns 503`: the request ran past the deadline or failed inside. Wait for `Retry-After` (10 seconds) and retry. Each response carries an `X-Request-Id`.
  - `## Still stuck`: the help line. Include the `X-Request-Id`.

### 4.5 API reference tab: hand-written pages

#### `api/openapi.mdx`: API overview

- **description:** Spec, base URL, errors and versioning for the Private Clubs API
- **Length:** 250 to 400 words, 3 to 4 code blocks.
- `## Spec URL`: `https://www.theprivateclubs.com/openapi.json` in a `text` block. Base URL `https://www.theprivateclubs.com`. No `Note`: the one that listed spec defects was dropped on 2026-10-18, because spec 1.2.0 (PR #679) fixes all of them.
- `## Endpoint`: one supported endpoint, `GET /api/v1/clubs`. Link the generated page at `/api-reference/clubs/search-clubs` (the group is in docs.json, section 5). Any other `/api/v1` path returns 404 with code `not_found` (live body: detail `This /api/v1 path does not exist.`, hint `Use GET /api/v1/clubs or read https://www.theprivateclubs.com/openapi.json`).
- `## Call it from your server`: REST sends no CORS headers, so browser calls from another origin fail. The MCP server allows any origin.
- `## Errors`: an RFC 9457 `application/problem+json` field table (`type`, `title`, `status`, `detail`, `code`, `hint`, `instance`) and a code table: `invalid_page` 400, `invalid_limit` 400, `not_found` 404, `clubs_unavailable` 500 (the directory could not be read, nothing else). Link `/problems`. Do not list 429. Add a `## Response headers` table: `X-Request-Id`, `RateLimit`, `RateLimit-Policy`, plus `X-Total-Clubs`, `X-Data-Source: Private Clubs` and `Cache-Control` on success.
- `## Versioning`: verbatim from the spec `x-versioning.policy`: "Breaking changes ship under a new /api/vN path. Deprecated versions send Deprecation and Sunset headers for at least 90 days."
- `## What you can do with the spec`: back since spec 1.2.0 (PR #679): bullets (generate a typed client, import into Postman or Insomnia, give it to an agent), plus `curl -o clubs-openapi.json https://www.theprivateclubs.com/openapi.json`.

#### `api/authentication.mdx`: Authentication and fees

- **description:** No keys or accounts, and what fee data each caller sees
- **Length:** 250 to 400 words.
- `## No API keys`: neither the MCP server nor the API takes a key, an Authorization header or an account. Leave out sign-in, OAuth and keys entirely.
- `## Fee access`: `fee_access` is `locked` for every API call (no figures). The values `band` (a range) and `exact` (club-verified figures) apply only to people signed in on theprivateclubs.com in a browser. Do not explain plans or how to get `exact`.
- `## Fee fields`: `initiation_fee`, `annual_dues` and `monthly_dues` are `null` when locked. `fee_views` has `initiation`, `annual`, `monthly` and `firstYear`, each with a `kind` of `none` (no figure stored), `locked`, `band` or `exact`. `fee_tier` (`verified`, `estimate`, `none`) labels where a figure comes from; everyone receives it, and it is never a figure.
- `## Fees over MCP`: the fees line, plus the tool fee message.
- `## Caching`: `Cache-Control: private, no-store` on every response.

#### `api/usage-limits.mdx`: Pricing and limits

- **description:** What the Private Clubs MCP server and API cost, the data license, page sizes and the request limits on each surface
- **Length:** 350 to 600 words. **Components:** 1 or 2 tables.
- `## Pricing`: calls to the MCP server and the API are free and need no account. Members use Private Clubs free. Business plans for clubs, brands and creators are on https://www.theprivateclubs.com/pricing; link there and quote no prices.
- `## Data license`: free use under `/data/license` and terms section 8: look up, search and compare clubs, including for work, with credit and a link.
- `## Bulk data`: bulk access is a paid monthly data feed license. List the bulk uses from terms section 8, say to email support@theprivateclubs.com, and link `/data/license` and `/legal/terms`. No price, plan name or SLA.
- `## API page size`: `limit` defaults to 50, the maximum is 50 and larger values are clamped. `page` starts at 1, and `page` times `limit`, with `limit` clamped to 50, can be at most 500 (403 `data_feed_required` past that, whatever the `total`). Both are whole numbers from 1 (`0` returns 400, leading zeros are fine). A page past the last result, within the first 500, returns 200 with an empty `clubs` array and the real `total`.
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
  - `waitlist_status`: the six values spec 1.2.0 lists, plus `null`.
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
- `## Current types`: a Type, Browse page, API `type=` also matches table, sourced from `src/lib/browse-type-scope.ts` and `src/middleware.ts`. The types are `golf` (+ `country_club`), `city` (+ `social`, `arts`, `cigar`, `dining`), `yacht`, `athletic` (+ `tennis`, `wellness`), `beach`, `equestrian`, `ski`, `automotive`, `aviation`, `outdoor` (+ `hunting`, `fishing`, `shooting`, `adventure`), `business`, `workspace`, `wellness`, `dining` (+ `wine`, and `type=wine` reads as `dining`, PR #679) and `residential`. The browse page is `https://www.theprivateclubs.com/clubs/{type}`.
- `## How the API and MCP match types`: the API groups as in the table. The MCP `type` is an exact match on the stored type, so `golf` there does not include `country_club`. The MCP tool description lists 13 types.
- `## Legacy types`: a Legacy, Now table: `social`, `arts`, `cigar` to `city`; `wine` to `dining`; `tennis` to `athletic`; `hunting`, `fishing`, `shooting`, `adventure` to `outdoor`; `country_club` to `golf`. `/clubs/{legacy}` answers 308 to the new page (live: `/clubs/tennis` to `/clubs/athletic`), and `/clubs/type/country_club` answers 308 to `/clubs/golf`.
- `## Browse URLs`: `/clubs/{type}` is canonical. Do not teach `/clubs/type/{type}`.

#### `reference/conventions.mdx`: Conventions

- **description:** How Private Clubs writes places, types, waitlist values and links, and how to cite it
- **Length:** 300 to 500 words.
- `## Places`: states and countries are full names. Two-letter state codes resolve in both MCP and the API. In the API, a state with no country stays in the US and Canada.
- `## Types`: snake_case. Link `/reference/club-types`.
- `## Waitlist values`: a value, meaning table. `open` means the club is listed as open to new members. `waitlist` means it is listed with a waitlist. `by_invitation` and `invite_only` both mean by invitation. `closed` means it is listed as not accepting new members (the app's own wording, `src/lib/ai/system-prompt.ts`). `unknown` means not known. Keep the meanings plain and add nothing more.
- `## Profile links`: the MCP `url` format. API rows carry no link. Do not teach building links by hand.
- `## Fees`: never state or estimate a club's fees (live `/llms.txt`).
- `## Missing values`: `null` means not known. Leave the field out of what you show, or point people to the club's profile. Never invent a value.
- `## Attribution`: the attribution line, plus "Data from Private Clubs (theprivateclubs.com) shows..." as a pattern, the license's own wording since PR #679. Link the license for the exact format.

#### `reference/discovery-files.mdx`: Discovery files

- **description:** The public files that tell AI apps and crawlers where to find the Private Clubs MCP server and API
- **Length:** 250 to 400 words. **Components:** 1 table.
- `## Files`: a File, What it holds table:
  - `https://www.theprivateclubs.com/mcp.json`: server name Private Clubs, version, logo (`icons`), endpoint, transport, the five tools, server key `private-clubs`, auth none, setup page.
  - `/openapi.json`: the REST spec.
  - `/llms.txt`: site overview for AI tools, regenerated hourly.
  - `/mcp/setup.md`: setup steps an agent reads and follows for its own app.
  - `/ai/context.md`: a short Markdown summary for AI tools. Since PR #679 it says Private Clubs and names `/api/v1/clubs` as the club data API.
  - `/data/license`: the data license page, which names Private Clubs and support@theprivateclubs.com since PR #679.
  - `/robots.txt`: allows AI crawlers.
- `## For AI crawlers`: robots.txt allows every user agent. It names AI crawlers such as GPTBot, ClaudeBot, PerplexityBot, Google-Extended and OAI-SearchBot. Copy the names from the live file. It may quote the catch-all group's `Content-Signal: search=yes, ai-input=yes, ai-train=yes` line: PR #689 (commit c4034ff2 on `feat/legal-mcp`) keeps robots.txt and the `ai-training: allowed` meta tag as they are and narrows terms section 8 and `/data/license` to match. Crawlers that follow robots.txt may read the public pages for search, AI answers and AI training; only AI training data collected through the MCP server or the API needs the data feed license. If Chris sets `ai-train=no` instead, this page, terms section 8 and `/data/license` change together.
- `## Which file to use`: one line each.
- **Leave out:** `/llms-full.txt` (stale), `/ai/clubs.json` (empty), `/agents.json` and `/ai-plugin.json` (not checked).

---

## 5. API endpoints group: generation and blockers

docs.json group (added 2026-10-18, after PR #679 shipped every fix below):

```json
{ "group": "API endpoints", "openapi": "https://www.theprivateclubs.com/openapi.json", "pages": ["GET /api/v1/clubs"] }
```

This is the shape Mintlify documents: a navigation group takes `openapi` (a repo path or a public URL), and `METHOD /path` entries in `pages` generate endpoint pages from it (mintlify.com/docs/api-playground/openapi-setup and /organize/navigation). Mintlify fetches the live spec at build time, and a change to a URL-hosted spec does not redeploy the docs by itself. Only `GET /api/v1/clubs` is listed, so `/ai/context.md`, `/llms.txt`, `POST /api/mcp` and `/api/clubs` get no pages. The spec's `x-mint` pins the page at `/api-reference/clubs/search-clubs`; `mint dev` served it there on 2026-10-18 and `mint broken-links` passed. The hand-written pages stay.

**Blockers before the API reference tab is public: all fixed.** PR #679 (merge 84e3d856) took `src/lib/openapi-spec.json` to v1.2.0, live at /openapi.json on 2026-10-18:

1. **Fixed.** `limit` defaults to 50 with a maximum of 200 (50 from `feat/api-bulk-cap`, spec 1.3.0) and a minimum of 1 on both `/api/v1/clubs` and `/api/clubs` (the shared `Limit` parameter, matching `CLUBS_API_DEFAULT_LIMIT` and `CLUBS_API_MAX_LIMIT` in `src/lib/api-v1.ts`).
2. **Fixed.** The description calls `RateLimit` and `RateLimit-Policy` advisory headers, and the spec lists no 429 anywhere.
3. **Fixed.** One server, `https://www.theprivateclubs.com`.
4. **Fixed.** `tags: ["Clubs"]`, summary `Search clubs` and `x-mint: { href: "/api-reference/clubs/search-clubs" }`.
5. **Fixed.** `country`, `q` and `waitlist` (comma list) are parameters.
6. **Fixed.** The `Club` schema has all 27 fields, and the `waitlist_status` enum is `open`, `waitlist`, `by_invitation`, `invite_only`, `closed`, `unknown` and `null`.
7. **Fixed.** `/ai/clubs.json` is out of the spec. (The route still returns zero clubs live, so the docs still leave it out.)
8. **Fixed.** `info.title` is "Private Clubs API" and `info.contact.email` is support@theprivateclubs.com.

The fallback (a corrected copy in this repo) is no longer needed.

---

## 6. docs.json changes (applied)

| Key | Before | After |
|---|---|---|
| `theme` | `willow` | `luma` (2026-10-18; was `mint`): the closest to the Wander look, with pill tabs and buttons, a calm white page and a narrow text column |
| `name` | The Private Clubs | `Private Clubs` (2026-10-18; briefly `Clubs`) |
| `description` | none | Documentation for the Private Clubs MCP server and REST API |
| `colors` | primary `#171717` | the site's blue: primary `#2563EB` (blue-600), light `#60A5FA` (blue-400, the dark-mode accent), dark `#2563EB` (buttons) |
| `background` | none | the site's ground: light `#FAFAFA`, dark `#0B0B0B` |
| `logo` | `/logo/light.svg`, `/logo/dark.svg` | `/logo/light.svg` (black) and `/logo/dark.svg` (white), href `https://www.theprivateclubs.com`. Both are the site's monogram and "Private Clubs" wordmark, built from `public/brand/tpc-logo-light.svg` and `tpc-logo-dark.svg` with the wordmark outlined from Instrument Sans so it renders the same in an `<img>` |
| `favicon` | DuckDuckGo URL | `/favicon.png`, the site's favicon (`public/favicon.png`) |
| `icons` | none | `{ "library": "tabler" }` |
| `fonts` | none | Instrument Sans, headings 550 and body 450, as the site sets them |
| `appearance` | none | `{ "default": "system" }` |
| `styling` | none | `{ "codeblocks": "system" }`, so code sits on white panels in light mode, as on `/mcp` |
| `navbar.links` | Support to mailto:data@theprivateclubs.com | Support to `mailto:support@theprivateclubs.com` |
| `navbar.primary` | "Visit Platform" to theprivateclubs.com | "Open Private Clubs" to `https://www.theprivateclubs.com/mcp` |
| `contextual` | none | options `copy, view, chatgpt, claude`, display `header`. `mcp` and `cursor` are left out because both install Mintlify's docs MCP server, which readers would confuse with the Private Clubs MCP |
| `navigation` | 2 tabs, 29 pages | the IA in section 3 |
| `footer.socials` | x | x, instagram, linkedin, youtube, website (from `src/components/home/atlas/homepage-footer.tsx`; TikTok is left out because it is not a known Mintlify social key) |
| `errors.404` | none | "This page does not exist or has moved" / "Check the link, or search the docs" |

`redirects` (added 2026-10-18): docs.theprivateclubs.com served the older docs, so each of the 27 retired paths in section 7 redirects (308) to its closest new page. `members/*` go to `/guides/members` and `clubs/*` to `/guides/clubs` (`members/comparing-clubs` and `clubs/claiming-your-listing` to the matching section), except `clubs/ai-discovery`, which goes to `/mcp/getting-started`. `platform/how-it-works` goes to `/how-clubs-works` and `platform/club-types` to `/reference/club-types`. `api/mcp-*` go to the matching `mcp/` page, and the other `api/*` paths go to the matching `api/` or `reference/` page (`api/get-club` to `/mcp/tools#get-club-details`, `api/fee-data` to `/api/authentication`, `api/statistics` to `/introduction`). `pricing/*` go to `/introduction`, because no page here lists plan prices.

`navigation` (2026-10-18, after PR #679): the `API endpoints` group (section 5) closes the API reference tab, generated from https://www.theprivateclubs.com/openapi.json.

Not set on purpose: `seo.metatags.canonical` (add `https://docs.theprivateclubs.com` once the new docs are live there) and `banner`.

`style.css` (loaded by Mintlify on every page) carries the website's sizes and surfaces: the `/list` and `/mcp` type scale (title 34, 44 then 52px; section heads 28 then 36px; body 16 on 24px in `#636363` or `#8E8E8E`), blue selected states, the blue pill primary, 20px white cards with the site's shadow, the blue icon tile and step badges, WanderNotice notes, white code panels and no eyebrow above page titles.

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

Each deleted path redirects to its closest new page (section 6, `redirects`), because docs.theprivateclubs.com served these pages before.

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
- `README.md`: a short description of the Private Clubs docs and how to preview with the Mintlify CLI. Remove the `npx skills add` block and the starter-kit copy.

---

## 9. Fact sheet (checked 2026-10-18)

### 9.1 MCP server

- Address `https://www.theprivateclubs.com/api/mcp`. serverInfo name and title `Private Clubs` (`MCP_SERVER_NAME` in `src/lib/mcp/config.ts`, PR #679), version `1.1.0`, icon `/logo.png` (512 px PNG, `mcpServerIcons`). Tools only (no resources or prompts). Stateless Streamable HTTP, POST only, JSON responses. GET and DELETE return 405 `Method not allowed, send MCP requests as POST` with `Allow: POST, OPTIONS`. CORS `Access-Control-Allow-Origin: *`. Every POST response carries `X-Request-Id` (`req_` and 16 hex characters). The 405 for GET and DELETE and the OPTIONS preflight carry none. No auth, account or key. (`src/app/api/mcp/route.ts`, `src/lib/mcp/harness.ts`; serverInfo read from the code, since the checks after #679 sent no POST)
- Tools (`tools/list`, from `route.ts`): `search_clubs` titled "Search Private Clubs"; `get_club` "Get Club Details"; `get_clubs_by_state` "Get Clubs by State" (description still says "in a specific US state", open item 5); `compare_clubs` "Compare Clubs"; `get_membership_info` "Get Membership Info".
- `search_clubs` query (`src/lib/mcp/club-search.ts`, `splitCityRegion`, PR #679): a query with a comma searches its first part only. A last part naming a US state or Canadian province (full name in any case, or a two-letter code in capitals, such as `FL`) filters by that state; else a country the directory knows filters by country; a code or name that is both is the state (`Toronto, CA` is California, `Tbilisi, Georgia` the US state; `Tbilisi, GE` is the country); a two-letter last part with any lowercase letter (`fl`, `Fl`, `in`) and a last part that names no place are dropped; a passed `state` wins. The old failure on a comma (`Club search failed. Try again.` every time) is gone: the term is quoted (`clubSearchOrFilters`).
- `compare_clubs` `location` (`clubLocation`): city and state, the country when there is no state, missing parts left out, `null` when all three are missing. No more `Sydney, null`.
- Order (`src/lib/mcp/club-ranking.ts`): `search_clubs` and `get_clubs_by_state` sort by Top 100 rank, priority score, Google review count, then name ("best known first"), and fold a club listed twice in one city and state (same name once generic words and the city's words are set aside, in any order) to its best ranked row. A row with no city, or a name with nothing left once those words are set aside (`Boca Raton Golf Club` in Boca Raton), never folds.
- Limits: body 2 MB; 100 messages per batch; `initialize` sent alone; unsupported `Mcp-Protocol-Version` returns 400; deadline 50 s, then 503 with `Retry-After: 10`. No request rate limit. (`harness.ts`)
- `/mcp.json`: name Private Clubs, worldwide, version 1.1.0, `icons` (the logo), five tools, `servers: [{key: private-clubs, url, transport: streamable-http, auth: none}]`, setup `https://www.theprivateclubs.com/mcp`. (live, 2026-10-18 after #679)
- Signed-in server: `POST /mcp` returns 503 `mcp_disabled`. Do not document it.

### 9.2 Setup

- Nine apps, in this order: Claude, ChatGPT, Claude Code, Codex, Cursor, Grok, Perplexity, OpenClaw, Hermes (`MCP_APP_IDS`). Agent apps: Claude Code, Codex, Cursor, OpenClaw, Hermes. Chat apps: Claude, ChatGPT, Grok, Perplexity.
- Plugin ZIPs (live, 200, PR #679): `/mcp/private-clubs.claude-plugin.zip`, `/mcp/private-clubs.codex-plugin.zip`, `/mcp/private-clubs.cursor-plugin.zip`. The old `/mcp/clubs.*-plugin.zip` paths answer 308 to them (`next.config.mjs`, live). Each plugin is named `private-clubs` and holds the MCP connection under the key `private-clubs` and the skill `skills/private-clubs/SKILL.md` (name `private-clubs`); the Claude and Codex ZIPs also carry `assets/logo.png`. The Cursor plugin is extracted to `~/.cursor/plugins/local/private-clubs`. There is no Claude Code marketplace (`MCP_MARKETPLACE` is null).
- Older entries: setup.ts says "Added Clubs before? Replace that older entry with Private Clubs" in chat apps, "Added clubs before? Replace that older entry with private-clubs" for Cursor and Hermes, and "Added clubs before? Run claude|codex|openclaw mcp remove clubs first" for the CLIs.
- Verification: only the Claude plugin flow was tested end to end (2026-10-18, under the old ZIP name). The Codex and Cursor plugin steps come from vendor docs and mirror `/mcp`; test them before publishing.

### 9.3 REST API

- `GET https://www.theprivateclubs.com/api/v1/clubs` re-exports `GET /api/clubs` and adds `X-Request-Id` (`req_` and 16 hex characters) to every answer, rows or a problem (`src/app/api/v1/clubs/route.ts`). No auth, no CORS. Response `{clubs, total, fee_access}`. Success headers: `Cache-Control: private, no-store`, `X-Total-Clubs`, `X-Data-Source: Private Clubs` (`API_DATA_SOURCE`), `RateLimit`, `RateLimit-Policy`. Problem responses carry `RateLimit` and `RateLimit-Policy`. (`src/app/api/clubs/route.ts`, `src/lib/api-v1.ts`, `src/lib/api-problem.ts`, live)
- Documented parameters: `type` (grouped), `state` (full name or two-letter code), `country` (name or ISO code), `q` (part of the name), `waitlist` (comma list; spec values `open`, `waitlist`, `by_invitation`, `invite_only`, `closed`, `unknown`; `by_invitation` also matches `invite_only`), `page` (from 1), `limit` (default 50, max 200, clamped; max 50 from `feat/api-bulk-cap`). `page` and `limit` must be whole numbers from 1 when sent (an empty `page=` or `limit=` counts as not sent): `0` answers 400 `invalid_page` or `invalid_limit`, and leading zeros are fine (`01` is 1). A page past the last one, however large (`page=99999999999999999` included), answers 200 with an empty `clubs` array and the real `total`. From `feat/api-bulk-cap`, `GET /api/v1/clubs` answers 403 `data_feed_required` when `page` times the clamped `limit` is above 500, before any read; a page past the last result within 500 still answers 200 empty. Verified on a local build of 3f773c0b on 2026-10-18: `page=10&limit=50` 200, `page=11&limit=50` 403, `page=10&limit=200` 200 with 50 rows, `page=0` 400 first. Verified live: `country=GB` and `country=United Kingdom` match the same clubs, `state=FL` matches the same as `state=Florida`, and `type=wine` returns the same clubs as `type=dining`.
- Types (`src/lib/browse-type-scope.ts`, PR #679): `city` groups `city`, `social`, `arts`, `cigar` and `dining`; `dining` and `wine` both group `dining` and `wine`.
- Exist in code but are not documented in v1 (unstable, or not in the spec): `city`, `amenity`, `architect`, `holes`, `sort`, `coming`, `ranked`, `tour`, `operator`, `homes`, `access`, `membership`, `perPage`, `feeMin`, `feeMax`.
- Errors (`API_PROBLEMS` in `src/lib/api-v1.ts`): `invalid_page` 400, `invalid_limit` 400 (hint `Call GET /api/v1/clubs?page=1&limit=50. Maximum limit is 200.`, and `Maximum limit is 50.` from `feat/api-bulk-cap`), `data_feed_required` 403 (from `feat/api-bulk-cap`, title `Bulk access needs a data feed`, names support@theprivateclubs.com; on 3f773c0b its `detail` and the spec spell "licence", while the terms and these docs spell "license", and `api/index.mdx` quotes the `detail` as the app sends it, so change that quote when the app's spelling changes), `not_found` 404 (any other `/api/v1` path, no `X-Request-Id`), `clubs_unavailable` 500 (only "The club directory could not be read", hint names support@theprivateclubs.com). Problem `type` URLs are `https://www.theprivateclubs.com/problems/{code}`, and each opens a page; `/problems` lists every code, and an unknown code is a 404. (live)

### 9.4 Fees

- MCP: never. REST: `fee_access` is `locked` for every API call (there are no API keys, only the site session). `band` and `exact` are for people signed in on the site. (`src/lib/fees/access.ts`, `src/lib/fees/api.ts`)
- Spec 1.2.0 `info.description` (`openApiDescription`): signed-out callers get no fee figures, free accounts get ranges, paying members get exact club-verified fees, and the anonymous Private Clubs MCP server returns no fees.
- Live `/llms.txt`: "Do not state or estimate a club's fees". `/ai/context.md` says the same and publishes no fee figures. A free account shows a price range on the site.

### 9.5 Pricing and license

- Members are free (`/pricing` FAQ). The MCP and API are free for public club data (`/mcp` FAQ). Business plans Plus, Pro, Elite and Max are listed on `/pricing`, and every plan button reads "Not available yet" today, so link `/pricing` and quote no prices.
- Data license (`/data/license`): PR #689 replaces the text below with three sections: citing and linking individual clubs at no charge with attribution, the attribution format (both examples kept), and bulk use and the paid monthly data feed license (support@theprivateclubs.com). Before #689 (PR #679): non-commercial use is free with attribution to Private Clubs, AI training is allowed with citation, the attribution formats are "According to Private Clubs (theprivateclubs.com), which indexes ... private clubs..." and "Data from Private Clubs (theprivateclubs.com) shows...", and commercial use, bulk access or integrations beyond the public endpoints go to support@theprivateclubs.com. The docs' attribution line follows it: "Cite Private Clubs (theprivateclubs.com) and link the club's profile."
- Claiming a club is free, with a work email and no credit card (`/list`).

### 9.6 Live use cases with verified arguments

Results were checked with `tools/call` on 2026-10-18. Writers re-run them before publishing.

| Audience | Ask (verbatim) | Tool | Arguments | Result today |
|---|---|---|---|---|
| Members | Which private golf clubs in Arizona have an open waitlist? | `search_clubs` | `{"state":"Arizona","type":"golf","waitlist":true}` | empty list (no Arizona golf club listed as open) |
| Members | Compare the two clubs I'm choosing between | `compare_clubs` | `{"slugs":["casa-cipriani-new-york","spring-place-new-york"]}` | 2 clubs |
| Members | Is the waitlist open at this club? | `get_membership_info` | `{"slug":"the-estancia-club"}` | found |
| Clubs | How does our club's profile read on Private Clubs? | `get_club` | `{"slug":"desert-forest-golf-club"}` | found |
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

1. **License versus "Free" chips:** closed by PR #689. Lookups inside AI apps and products are free, including for work, so the "Free" chips on `/mcp` match the license. Bulk use needs the paid data feed license.
2. **OpenAPI fixes (closed 2026-10-18):** PR #679 fixed all eight items in section 5 (spec 1.2.0, live), and the `API endpoints` group is in docs.json.
3. **Logo files (closed 2026-10-18):** `logo/light.svg` and `logo/dark.svg` are built from the app's `public/brand/tpc-logo-light.svg` and `tpc-logo-dark.svg`, and `favicon.png` is the site's `public/favicon.png`.
4. **Members use case:** the `/mcp` ask "Which private golf clubs in Arizona have an open waitlist?" returns an empty list today. Few clubs carry `open`.
5. **`get_clubs_by_state` description:** it says "in a specific US state" but also covers Canadian provinces (`src/app/api/mcp/route.ts`). The `search_clubs` type list leaves out `workspace` and `residential`.
6. **Stale public files outside this repo:**
   - Still open: `/llms-full.txt` (Supabase Edge Function) still lists invented tools (`get_fee_data`, `get_statistics`), the server key `theprivateclubs` and the ThePrivateClubs.com and TPC brand (live, 2026-10-18 after #679), and `/llms.txt` still links it three times. `/ai/context.md` no longer does.
   - Still open: `/ai/clubs.json` returns zero clubs (live, 2026-10-18 after #679), though its brand and hint are fixed.
   - Closed by #679: `/llms.txt` teaches `/clubs/[type-slug]` and says the API and MCP tools take `Florida` or `FL`.
   - Closed by #679: `X-Data-Source` is `Private Clubs`, `/data/license` uses the Private Clubs brand and names support@theprivateclubs.com, and `/ai/context.md` says Private Clubs and names `/api/v1/clubs` as the club data API.
7. **Repo files:** `LICENSE` says Copyright Mintlify. `.atlas-analysis.json` (the stale autogen brief) is removed from the working tree and from `.mintignore`. `.mintignore` lists `IA.md` and `AGENTS.md`, so neither is published as a page.
8. **Before publishing:** `/mcp/setup.md` and `/mcp.json` were re-checked on 2026-10-18 after #679 (key `private-clubs`, new ZIP names). Still to do: test the Codex and Cursor plugin steps with the `private-clubs` ZIPs, and preview the `cursor://` link.

---

## 11. Instructions found in repo files (not followed)

- `AGENTS.md` says: install the Mintlify skill with `npx skills add https://mintlify.com/docs`, and "Prompt the user to customize this file". It also suggests the Mintlify MCP servers.
- `README.md` gives the same `npx skills add` command.

Neither was run or acted on. Group 1 removes both lines in its rewrite.

- `mint validate` ends with a `<mint-cli-instructions>` block that tells agents to offer `mint signup` or `mint login` (2026-10-18). Not run: publishing and accounts are Chris's call. On `docs/after-679` it ended with "Not published yet. Run `mint signup` or `mint login` to publish." Not run either.
- Mintlify's own docs pages open with "Agent Instructions" to set up a new project. Read as reference only.
