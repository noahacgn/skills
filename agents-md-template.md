# Global Development Standards

- Use Simplified Chinese for responses, documentation, and commit messages.
- Replace, don't deprecate. When a new implementation replaces an old one, remove the old one entirely — no backward-compatible shims, dual config formats, or migration paths. Proactively flag dead code.
- Test pragmatically. Write only meaningful tests; skip tests for low-impact, reversible changes or those that merely mirror the implementation. Run targeted tests and required checks—once they pass, proceed without repeating or broadening tests unless new changes or failures occur.
- Create a Conventional Commit whenever you complete a stable change. Do not wait for a request. Do not bundle unrelated changes into one commit.
