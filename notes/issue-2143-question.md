# Question for maintainer — issue #2143 (`.deepagentsignore`)

**Context**

Issue #2143 proposes a `.deepagentsignore` file that applies gitignore-style exclusion rules
to all file-reading tools (`read_file`, `@` mentions, directory listings).

The existing architecture already separates `read` from `write` operations via
`FilesystemPermission` rules (see `libs/deepagents/middleware/filesystem/`).

**Question**

> Should `.deepagentsignore` be treated as **read-only to the agent itself** —
> i.e., should the permissions system hard-block any `write_file` / `edit_file`
> call that targets `.deepagentsignore` — or should the file remain editable
> by the agent like any other project file?

**Why it matters**

If the agent can overwrite its own ignore file it can silently remove its own
access restrictions, which undermines the security boundary the feature is
meant to create. Making it read-only to the agent (while still writable by the
human via the shell) aligns with how `.gitignore` is treated by tools like
Claude Code's `.claudeignore`.

**Ref:** https://github.com/langchain-ai/deepagents/issues/2143
