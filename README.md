# Private Clubs docs

The docs for the Private Clubs MCP server and REST API, built with [Mintlify](https://mintlify.com). They cover the free MCP server at `https://www.theprivateclubs.com/api/mcp` and the `GET /api/v1/clubs` endpoint.

## What is here

- `docs.json`: navigation, theme and settings
- `style.css`: the website's type, colours and surfaces, which Mintlify loads on every page
- `logo/`, `favicon.png` and `images/`: our logo, the site's favicon and the illustrations from `/mcp`
- `introduction.mdx` and `how-clubs-works.mdx`: getting started
- `guides/` and `use-cases/`: how-tos and example asks by audience
- `mcp/`: setup, tools and troubleshooting for the MCP server
- `api/` and `reference/`: the API reference tab and the data reference
- `IA.md`: the plan, with page outlines and the fact sheet
- `AGENTS.md`: rules for AI agents that edit these docs

The `API endpoints` group in `docs.json` generates the `GET /api/v1/clubs` page at `/api-reference/clubs/search-clubs` from the live spec at https://www.theprivateclubs.com/openapi.json. Mintlify reads the spec when it builds, so a spec change shows after the next deploy of these docs.

## Preview locally

1. Install Node.js 20.17 or later. Mintlify recommends an LTS release.
2. Install the Mintlify CLI:

   ```bash
   npm i -g mint
   ```

3. From the folder that holds `docs.json`, start the preview:

   ```bash
   mint dev
   ```

4. Open http://localhost:3000. To use another port, run `mint dev --port 3333`.

Before you open a pull request, check the links and the build:

```bash
mint broken-links
mint validate
```

If the preview does not start, run `mint update` and try again.

## Writing

Follow `AGENTS.md` for naming, house style and content boundaries, and `IA.md` for page outlines. Check every fact against the app code or a live call before you write it.

## Publishing

Pages go live only when Mintlify deploys this repo. Ask Chris before you push or merge.

## Help

Email support@theprivateclubs.com.
