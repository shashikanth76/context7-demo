# Context7 stack reference docs

Six small, self-contained, **stable** reference documents — one per
technology in the study's example architecture (React, Spring Boot,
PostgreSQL, Kafka, Kubernetes, OAuth/OIDC) — written in the
[llms-full.txt](https://llmstxt.org/) convention (plain markdown, no
external links required to resolve them, so a crawler doesn't need to
follow anything to get the full content).

## Why these exist

Context7 already indexes the real upstream docs for all six of these
technologies extremely well — nothing here is meant to replace that.
These exist because live upstream docs (and Tavily's live web search
results) can legitimately change between when you test the LangGraph
implementation and when you later test the ADK / Microsoft Agent
Framework implementations, which would make the framework comparison
not quite apples-to-apples. Each of these six docs is a small, deliberate,
version-pinned snapshot of current guidance the Research Agent can cite
consistently across every comparison run, on top of (not instead of)
whatever live results Tavily/Context7 return that day.

They also deliberately echo several of the tensions already built into
[`../filesystem/kb`](../filesystem/kb) (Hystrix vs. resilience4j, mTLS vs.
OAuth-only, multi-zone vs. multi-region, etc.) so the Research Agent has
something concrete and current to cite when the Architecture Review
Agent asks "does current external guidance agree/disagree with our
internal standard here?".

## Uploading to Context7 (free tier — public sources only)

Context7's free tier only indexes **public** sources (a public GitHub
repo, or a publicly-reachable `llms.txt`/`llms-full.txt` URL). To get six
*separate*, independently citable library IDs (one per file) rather than
one combined library, submit each file's raw URL separately via **Add
LLMs.txt**, not as one repo import:

1. Push this `mcp/context7-stack-docs/` folder to any public Git host
   (a small dedicated public repo, or even a public Gist per file, works
   fine — this content is not sensitive, it's synthetic reference
   material for the study).
2. For each of the six files, get its raw file URL, e.g.:
   `https://raw.githubusercontent.com/<you>/<repo>/main/react.llms.txt`
3. Go to <https://context7.com/add-library>, choose **Add LLMs.txt**, and
   paste that raw URL. Repeat for all six files.
4. Context7 will index each under its own library ID, typically
   `/llmstxt/<derived-name>` — note the six IDs it assigns (visible on
   each library's context7.com page, or via the
   [Search Library API](https://context7.com/docs/api-reference/search/search-for-libraries)).

Equivalently, via the API (see
[`mcp/README.md`](../README.md) for where `CONTEXT7_API_KEY` comes from):

```bash
curl -X POST https://context7.com/api/v2/add/llmstxt \
  -H "Authorization: Bearer $CONTEXT7_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"llmstxtUrl": "https://raw.githubusercontent.com/<you>/<repo>/main/react.llms.txt"}'
```
(repeat once per file, with each file's raw URL).

## If you have a Context7 Pro/Enterprise plan

You can instead add these as **private sources** (Sources tab → Add
another → Other Git / upload) without needing to host them on a public
repo first — see
<https://context7.com/docs/howto/private-sources>. Functionally
equivalent for this study; the free public-URL path above just needs
nothing beyond the API key you already have.

## After uploading

No code changes are needed in `agents/langgraph/research_agent/` — the
Research Agent's Context7 tool (`resolve-library-id` /
`query-docs`/`get-library-docs`) searches Context7's whole index, so once
these are live they're automatically discoverable the same way any other
library is, e.g. a query like *"current Spring Boot resilience
recommendations"* should be able to resolve to whichever library ID
Context7 assigned to `spring-boot.llms.txt`.
