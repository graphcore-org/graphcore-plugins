---
name: using-terminus-estimator
description: How Terminus Painting's estimator app works and how to use it through the terminus MCP server. Use when the user works with the estimator: its teams, quotes, rooms, walkthroughs, questions, photos, pricing, approvals, PaintScout push records, or a team's settings, rate book and quote text.
---

# Using the Terminus estimator

The estimator is Terminus Painting's quoting app. It holds each quote's walkthrough, rooms, questions and
photos, prices the quote with the team's rate book, and records approvals and pushes to PaintScout. You
reach it through the `terminus` MCP server.

This skill covers how the app and the connector work. It does not prescribe a way of making a quote, such
as what to ask, when to ask for approval, or what to report. That is up to the user and their team, who may
have their own skills for it. Unless one of those applies, do what the user asks, and ask them when you are
unsure.

## Connecting

Call `open("/")`. If the `terminus` tools are missing, or the call fails as unauthenticated, the user
has to sign in; you can't do it for them:

- **Claude Code:** run `/mcp`, pick the `terminus` server, and choose Authenticate.
- **Codex:** run `codex mcp login terminus` in a terminal.
- **Claude on the web or the Claude app:** add a custom connector with the URL
  `https://terminuspainting.graphcore.dev/mcp` under Settings → Connectors, then connect it.

A browser opens the Terminus Painting sign-in page. Ask the user to tell you when they're done, then call
`open("/")` again. The tools act as the person who signed in.

## Three tools

- `open(at)` reads a place. Each place is a page: what it shows, how the app behaves there, links to
  other places, a `version`, and its **Tools**.
- `call(at, tool, params)` runs a tool listed at that place. Call a tool at the place that lists it.
  Some operations work only there.
  Pass the `version` you last read as `seen_version`, so a write based on out-of-date content is
  refused rather than applied. If it is refused, open the place again and decide again.
- `find(type, predicate)` lists entities of a type, optionally filtered by field equality.

The places are the reference for the app. They say what each tool takes, does and refuses, and what a
change reaches. Read the place before acting on it, and trust it over anything you remember, including
this skill.

## How the app is organised

`open("/")` shows the app map and the user's teams. The map gives every address, so use it to find a
place rather than guessing:

- **Teams.** Every quote and setting belongs to a team, and only its members see them. Everyone on a team
  can do everything on it. Someone on no team sees nothing else. To join a team, a member makes an invite
  link on the team's Members page in the web app. There is no tool for invites. Only a deployment
  administrator creates teams.
- **A team** (its id) lists its quotes and members and has `start_quote`. Its base settings, rate book and
  quote text are places under `/estimator/<team id>/settings`. A new quote copies the base settings, and
  changing them later does not reach existing quotes.
- **A quote** (its id) is an overview: its stage, where it stands, and how a quote works. Its places are
  the web app's tabs: `/estimator/<quote id>/rooms`, `…/questions`, `…/walkthrough`, `…/price` and
  `…/history`. A room and a pricing run each open as a place of their own.

What a quote goes through, as the app enforces it:

- The stages are created, priced, approved and pushed, and the app sets each one.
- A pricing run is the price at one moment and never changes. A change to a room or the job details marks
  the price out of date, and `approve` and `get_push_payload` refuse until the quote is priced again.
- An approval belongs to one run and records who approved it and how. A team member can approve in the
  web app, and the Price place shows that.
- Every change records who made it, and History lists them.

## What the app does not do

- **It never calls CompanyCam or PaintScout.** Walkthrough records and photos come in through the app's
  tools. A push is a PaintScout draft that you write with the PaintScout connector, if the user has one,
  then record with `record_push`. `get_push_payload` returns the payload, and the Price place explains how
  it maps to PaintScout's calls.
- **It doesn't upload photos over MCP.** `upload_photos` returns a short-lived token and the request to
  send. Each photo is posted from a shell that can reach both the image and the app. Without a shell, the
  user adds photos in the web app. In the Claude app, `graphcore.dev` has to be an allowed site
  (Settings → Capabilities → Allowed sites).
- **It doesn't invite or manage people over MCP.** Membership is managed in the web app.

## The web app

Every place has a `web:` link that opens the same thing in the web app at `https://terminuspainting.graphcore.dev`.
Give it to the user when they would rather do something there, or when it can only be done there, such as
invites.
