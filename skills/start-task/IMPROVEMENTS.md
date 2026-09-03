## 2026-08-24
- Signal: friction
- What happened: Skill defaulted the base branch to `develop` and had no check on what it was branching from. In practice, the actual git sequence used was `git fetch origin <base> && git checkout <base> && git pull origin <base> && (git checkout -b <key> || git checkout <key>) && git branch --show-current`, and feature branches must always be cut from a `release/Sprint-<number>` branch, not `develop`.
- Suggested fix: default `<target-branch>` to the currently checked-out branch instead of hardcoding `develop`; warn explicitly if it doesn't match `release/Sprint-<number>` before creating the branch; use the exact git command sequence above.
- Resolved: 2026-08-24
