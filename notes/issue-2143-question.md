# Question for maintainer — issue #2143 (`.deepagentsignore`)

**Context**

Issue #2143 proposes a `.deepagentsignore` file (gitignore syntax) that tells
the agent to ignore certain paths. The existing `FilesystemPermission` system
already distinguishes `"read"` from `"write"` operations and enforces them at
tool call time (`read_file`, `write_file`, `edit_file`, `glob`, `grep`, etc.).

**Question**

> When a path matches `.deepagentsignore`, should the agent:
>
> **(A) Not see it only** — `read_file`, `grep`, `glob`, `@` mentions are
> blocked, but `write_file` / `edit_file` are still allowed?
>
> **(B) Neither see nor edit it** — all tool operations (`read` AND `write`)
> are blocked for matching paths?

**Why it matters**

These serve two different user needs:

- **"Not see"** is a *privacy / context guard* — keeps `.env`, credentials,
  and large build artifacts out of the agent's context window.
- **"Neither see nor edit"** is an *integrity guard* — prevents the agent from
  touching lock files, generated artifacts, or config the user never wants
  overwritten.

Most users probably expect both blocked (option B), but the issue body only
mentions file-reading tools. Clarifying this upfront avoids a confusing split
implementation later.

**Technically**, both options map cleanly to the existing `FilesystemPermission`
rule system — option A generates `deny read` rules; option B generates
`deny read + deny write` rules. The question is which semantic `.deepagentsignore`
is meant to express.

**Ref:** https://github.com/langchain-ai/deepagents/issues/2143
