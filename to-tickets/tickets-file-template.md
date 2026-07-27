# Tickets file template

Used when the configured tracker is local markdown — written to `.scratch/<requirement-slug>/tickets.md`, all tickets in dependency order (blockers first).

<tickets-file-template>

# Tickets: <short name of the work>

A one-line summary of what these tickets build. Reference the source spec if there is one.

Work the **frontier**: any ticket whose blockers are all done. For a purely linear chain that means top to bottom.

## <Ticket title>

**What to build:** the end-to-end behaviour this ticket makes work, from the user's perspective — not a layer-by-layer implementation list.

**Stories covered:** the numbered spec stories this ticket addresses (omit when the source has none).

**Blocked by:** the titles of the tickets that gate this one, or "None — can start immediately".

- [ ] Acceptance criterion 1
- [ ] Acceptance criterion 2

## <Ticket title>

...

</tickets-file-template>
