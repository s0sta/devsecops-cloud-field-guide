# GitHub profile achievements — the honest 2026 guide

Which profile badges are live, the real tier thresholds, and the ones you cannot
earn any more no matter how much activity you generate.

## Live badges

| Badge | Earned by | Tiers (default → gold) |
| --- | --- | --- |
| **Pull Shark** | merged pull requests | 2 / 16 / 128 / 1024 |
| **Starstruck** | stars on a public repository | 16 / 128 / 512 / 4096 |
| **Pair Extraordinaire** | co-authored merged pull requests | 1 / 10 / 24 / 48 |
| **Galaxy Brain** | accepted answers in GitHub Discussions | 2 / 8 / 16 / 32 |
| **YOLO** | merge your own pull request without a review | — |
| **Quickdraw** | close an issue or PR within 5 minutes | — |
| **Public Sponsor** | sponsor someone publicly through GitHub Sponsors | — |

## Not obtainable

| Badge | Status |
| --- | --- |
| Heart On Your Sleeve | disabled — experimental badge, briefly re-enabled by a 2026 backend error, then pulled back |
| Open Sourcerer | disabled — same story |
| Arctic Code Vault Contributor | retired — only the 2020 snapshot counts |
| Mars 2020 Helicopter Contributor | retired — only the 2021 mission snapshot counts |

The practical ceiling is **7 badges**, not 10. The two disabled ones have no
published criteria and no release date; the two retired ones can never be awarded
again.

## How to earn each live badge

### Pull Shark — merged pull requests

Open a pull request that gets merged. It counts in public repositories; private
repository work does not. The tier path is 2 → 16 → 128 → 1024 merged PRs.

Practical path: contribute small fixes (docs, typos, tests) to open-source
projects you already use, or make a side project public and ship changes through
pull requests instead of pushing straight to the default branch.

### Starstruck — stars on a public repository

Reach 16 stars on a single public repository. There is no shortcut: self-stars do
not count. The only path is publishing something useful and telling people about
it.

### Pair Extraordinaire — co-authored merged pull requests

Add a co-author trailer to a commit in a pull request that gets merged:

```
Co-authored-by: Name <their-github-email@example.com>
```

The email must match the co-author's GitHub account, otherwise the commit is not
linked to them and nothing is awarded.

### Galaxy Brain — accepted answers in Discussions

Answer questions in repositories that have Discussions enabled, and have your
answer marked as the accepted answer. Default tier needs 2 accepted answers.

### YOLO — merge without review

On a repository you own: create a branch, make a change, open a pull request,
merge it yourself without requesting a review.

### Quickdraw — close within 5 minutes

Open an issue or a pull request and close it within 5 minutes. Legitimate use:
open a follow-up issue, resolve it immediately, and close it with the fix.

### Public Sponsor — sponsor publicly

Become a public sponsor of any developer or project through GitHub Sponsors. A
public (visible) sponsorship of $1/month is enough; private sponsorships do not
count.

## What "x2" means on a badge

Tiered achievements show a small counter. `Pull Shark x2` means the **second
tier** (16 merged PRs), not two separate badges. Tier upgrades happen
automatically; there is nothing to claim.

## Sources

- GitHub Community Discussions, "Free available badges" — https://github.com/orgs/community/discussions/175547
- Community achievement guide, September 2026 status — https://github.com/SahilAkoliya-Dev/github-achievement-badges
- "GitHub Quietly Broke 2 Achievement Badges in 2026" — https://dev.to/sahil_akoliya_dev/github-quietly-broke-2-achievement-badges-in-2026-heres-what-actually-works-3a1d
