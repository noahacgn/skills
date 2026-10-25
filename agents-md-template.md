# Global Development Standards

- Use Simplified Chinese for responses, documentation, and commit messages.
- Replace, don't deprecate. When a new implementation replaces an old one, remove the old one entirely — no backward-compatible shims, dual config formats, or migration paths. Proactively flag dead code.
- Before adding complexity for a speculative or vanishingly unlikely race or security edge case, explain the concrete failure mode, likelihood, and complexity cost to the user and get their buy-in. Do not silently expand scope for theoretical robustness.
- Create a Conventional Commit whenever you complete a stable change. Do not wait for a request. Do not bundle unrelated changes into one commit.
