# Notion steps

The loop in `SKILL.md` names six moments where a Notion page needs more. The page lives in
Notion; the loop's file is a snapshot of it, and git diffs the snapshot. The tools are those of
Notion's MCP server: `fetch`, `update-page`, `get-comments`, `create-comment`, and
`set-discussion-resolved`. If your agent has no Notion connection, say so and stop.

## Start

Fetch the page by the URL or ID the user gave. Take a short slug from its title. The snapshot is
`${TMPDIR:-/tmp}/iterate-<slug>/<slug>.notion.md`; write the path out in full each time. Use the
Shadow setup from `git.md`, with the snapshot's directory as the work tree. Then Pull.

## Pull

Fetch the page, without `include_discussions`. Write the snapshot: the line `title: <title>`, a
blank line, then everything between `<content>` and `</content>`, copied exactly. Only a Pull
writes the snapshot, and a Pull comes before every commit, so each commit holds the page as
Notion stores it. Notion normalizes what it is given; the snapshot records what it kept.

Text with a comment on it comes back wrapped in `<span discussion-urls="…">`, resolved or not.
Keep those spans: a new one in the user's diff shows where they commented.

## Hand off

Give the user the page's Notion URL instead of a file path. Tell them to edit the page, and to
leave directives either way:

- Type `@AI: <command>` into the page. Typing `@` opens Notion's mention menu; press Escape
  after `AI` and keep typing.
- Select text and comment on it, starting the comment with `@AI:`.

## Edit

Change the page with `update-page`, command `update_content`: search-and-replace, with an
`old_str` copied from the snapshot. Never use `replace_content`; it rewrites the page and can
drop the user's latest edits, comment anchors, and child pages. If an edit fails with "No
matches found", the user changed the page since the last Pull: Pull, and redo the edit. Change
the title with `update_properties`.

Pull after any edit that changes structure, and read what Notion made of it: it merges adjacent
lists, for one.

## Find directives

Two kinds:

- Inline: `grep -n '@AI' <snapshot>`. People type `@AI:`, `@AI -`, or `@AI` and a space. A
  directive is about the block it's in; standing alone, it's about where it sits.
- Comments: `get-comments` with `include_all_blocks`. Each unresolved discussion whose first
  comment starts with `@AI` is a directive about its `text-context`. A discussion on the page
  itself is about the whole page.

Remove an inline directive with `update_content`, as part of carrying it out. For a comment,
reply in its discussion with what you did, in a sentence, then resolve it with
`set-discussion-resolved`; the user asked for that when they chose this loop. Resolving needs a
Notion Business or Enterprise plan; if it fails, leave the reply and ask the user to resolve the
thread. `by-you` doesn't tell your comments from the user's, because the connection acts as
them; resolution is what marks a directive done.

## End

Pull and commit. List the directives still open, inline and in comments, so the user knows what
saying "good enough" left. Tell them the shadow repository's path, in case they want the
turn-by-turn history.
