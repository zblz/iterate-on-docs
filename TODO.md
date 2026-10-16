# TODO

## Next

- [ ] `notion` Iterate on a Notion page, through Notion's MCP server (fork only; upstream lists
  formats beyond markdown and HTML as a non-goal)
  - Design: same loop, with a `notion.md` beside `html.md` and **Notion:** marks in `SKILL.md`.
    Each turn, fetch the page into a snapshot (`<page-slug>.notion.md`) in a Shadow repo; git
    diffs the snapshot. Only a fetch writes the snapshot; commit the fetched form, never the
    agent's own idea of it (Notion normalizes, like the HTML Tidy step).
  - Edit with `update-page` / `update_content` (targeted search-and-replace), never
    `replace_content`. A stale `old_str` fails with "No matches found": fetch again and retry.
    Fetch again after structural edits: Notion merges adjacent lists.
  - Directives, both kinds: inline `@AI:` text in the page (comes back as plain text), and
    unresolved comment threads whose first comment starts with `@AI:` (about the block in
    `text-context`). After carrying one out, reply with what was done, then resolve the thread.
  - `by-you` is true on the user's comments too (the connection is the user), so it can't tell
    the agent's comments apart. Resolving is what marks a thread done.
  - Commented text shows as `<span discussion-urls="…">` in every fetch, resolved or not. Kept
    in the snapshot: a new span in the user's diff shows where they commented.
  - Resolving needs a Business or Enterprise plan. Without it, reply and ask the user to resolve.
  - Typed in the Notion UI, `@AI` needs Escape to dismiss the mention menu, then comes back as
    plain text. People write `@AI -` too, so match `@AI`, not `@AI:`.
  - Still to check: the user and the agent editing at once; page-level comments; a long page
    (the agent copies the fetched text into the snapshot by hand; does it stay exact?).
  - Spike page: "iterate-on-docs spike (scratch)", a private draft in Notion.
  - Written: `notion.md`, marks in `SKILL.md`, `git.md` (a Notion page uses Shadow), README,
    plugin descriptions. Next: a full run on a real page, then release.
- [ ] get the README up to date
- [ ] `turn-commits` Decide what happens to iterate-on-a-document's per-turn commits
  - Surfaced while considering a "squash at the end" instruction for the skill.
  - The turn commits make the loop work (`git diff HEAD~1` separates the user's edits from the
    agent's), but most people may not want them in their repo's history.
  - Options: (1) leave as-is; (2) offer a squash at "good enough" (record the start commit; skip
    if pushed or interleaved with other work); (3) shadow git dir outside the project
    (`--git-dir`/`--work-tree`), so the file stays in place, the real repo sees one diff, and no
    repo is required; (4) iterate on a branch and end with a kept merge commit (Jess's favorite).
  - People's git processes vary. For now, `skills/iterate-on-a-document/git.md` offers options
    2–4 plus in-place, and the agent picks from signals in the repo. Get feedback from more users
    on whether its picks fit them.
- [x] can we simplify the iterate-on-a-document skill? ask the agent. (HTML steps moved to html.md; the server takes the file)

## Backlog

- [x] Rename html-with-comments to html-revisor
- [ ] get someone to proofread the README and tell me whether it makes sense
- [x] test installing the plugin somewhere that doesn't have it locally (Avdi tested it; it worked)
