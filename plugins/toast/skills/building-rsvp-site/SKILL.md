---
name: building-rsvp-site
description: Guides a couple through setting up their wedding in Toast and building its RSVP guest site, using the toast MCP server. Use when the user wants to create, set up, check on, or change their Toast wedding, wedding website, RSVP page, events, or guest list.
---

# Building a Toast RSVP wedding site

Toast holds a couple's wedding: its events, its guest list, and a guest site
where guests find their invitation and RSVP. You work through the `toast`
MCP server's tools: `open(at)` reads a place, and `call(at, tool, params)`
runs a tool listed under that place's Tools. Every place tells you what it
shows and what you can do there, so read it before acting.

## 1. Make sure the user is signed in

Call `open("/")`. If the toast tools are missing, or the call fails as
unauthenticated, the user has to sign in. You can't do it for them. Tell
them how, for the app they're using:

- **Claude Code:** run `/mcp`, pick the Toast server, and choose Authenticate.
- **Codex:** run `codex mcp login toast` in a terminal.

A browser opens the Toast sign-in page, where they sign in or create an
account. Ask them to tell you when they're done, then call `open("/")` again.

## 2. Check for weddings

`open("/")` lists the user's weddings.

- **One wedding:** open it and go to step 3.
- **Several:** ask which one, then go to step 3.
- **None:** go to step 4.

## 3. Get up to speed on an existing wedding

Open the wedding. Its overview shows the date, events, RSVP tallies, parties
who haven't responded, failed emails, and the guest site's state, with
addresses for the Events, Guests, Quiz, Site and Settings tabs. Open the tabs
you need for the full picture.

Then give the user a short summary: the couple and date, the events, how many
guests and how many have responded, and whether the guest site doesn't exist
yet, is a draft, is published, or has unpublished changes. Suggest the
obvious next step (add events, add guests, create or publish the site) and
ask what they want to work on.

## 4. Create a new wedding with the user

Ask a few questions at a time, conversationally, not as a form. Never make
up details; ask for them.

1. **The wedding.** Ask for:
   - the couple's names as guests should see them ("Jackson & Jessica")
   - the date
   - where it is
   - an RSVP deadline (optional)

   Work out the IANA time zone from the location and confirm it. Read the
   details back, then call `create_wedding` at `/` and open the new wedding.
2. **Events.** Ask what guests will RSVP to: ceremony, reception, rehearsal
   dinner, welcome drinks. For each event, ask for:
   - a start time (an end time is optional)
   - the venue name and address
   - attire

   Add each one with `create_event` on the wedding's Events tab. Times are
   ISO 8601 with the wedding's UTC offset.
3. **The guest site.** Open the wedding's Site tab and follow its "How to
   build the site" section: ask what feel they want, pick the closer
   template, agree on the site's address (its slug), and create the site.
4. **Guests.** Offer to add guests on the Guests tab, one party per
   invitation.

At the end, summarize what exists and what's left before guests can RSVP.
Guests see the site only once it's published.
