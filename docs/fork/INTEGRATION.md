# Personal fork integration workflow

This fork carries a controlled integration lane for Strix Halo fixes and selected public mainline improvements. It also retains independent branches for contributions to the community repository.

## Branches

| Branch | Role | Initial state, 2026-10-10 |
| --- | --- | --- |
| `strix-upstream` | Unmodified community Strix baseline. Update explicitly after reviewing upstream history. | Community master `6aa81b88e14aa753c1c7e1a9ff27c9d50fd3c673`. |
| `integration` | Combined candidates under test. Never assume this branch is suitable for daily work. | Same community baseline plus this documentation; no additional fixes integrated. |
| `validated` | Deployment reference. Move only after an explicit promotion with evidence and owner approval. | BOOTSTRAP ONLY: legacy daily toolbox source `8c1c282ecb194e8f02613defcc4a07c22b6d1c08`, identified from its versions.txt. Not freshly validated or a quality endorsement. |

The existing default `master` and all PR branches are preserved. Tracking upstream does not deploy anything. The initial `validated` branch records legacy source history; the retained container and launcher, not a new rebuild of that commit, are the deployment rollback artifacts. The exact image identity and launcher snapshot are recorded locally.

## Add one change

1. Check current Strix master, open PRs and related public mainline changes for duplicates, dependencies, provenance and interactions.
2. Develop each independent fix on its own branch from an appropriate pinned Strix baseline. Do not base an upstream PR on the combined integration branch unless the dependency is intentional and disclosed.
3. Review the exact source commits, including scripts and workflows. Bring one selected change into integration at a time, preserving authorship. Use a merge when appropriate or `cherry-pick -x` for a selected subset. Record manual conflict resolutions.
4. Record source URL, full source SHA, integration SHA, baseline SHA, affected paths and semantic intent in the change ledger. Author attribution alone does not establish provenance.
5. Run focused regression and correctness checks before adding the next change. Keep failed experiments out of promoted builds; revert them without rewriting shared history.
6. Revalidate the combined build. Evidence from an isolated source PR does not establish correctness or performance of the combination.

Mainline changes are selected candidates, not an automatic merge of all llama.cpp changes. Inspect compatibility with the fork's QSA, MTP, cache and backend modifications. Respect this repository's clean provenance and contribution scope rules.

## Promotion to daily use

Follow AGENTS.md and CONTRIBUTING.md. Freeze full SHAs, source diff, compiler, image digest/ID, model hashes, runtime settings and evidence before promotion.

- Math-preserving changes require the repository's deterministic correctness gates, including token IDs and complete logits where required. Do not hash responses containing random request/tool IDs as a substitute.
- Intentional numerical or selection changes need a separately documented quality evaluation and explicit acceptance; faster output is not proof of equal quality.
- Cover affected backends and operations, production MTP and serial decode, long contexts and relevant boundaries, screenshots, concurrent conversations, rollback, cache restore and slot reuse.
- Run the required backend/perplexity and benchmark gates. Measure decode, prefill, acceptance, memory and storage effects using equivalent pinned builds and repeated samples.
- Include real coding workloads with functional checks. Use TARGET PASS, GLOBAL PASS, FAIL or INCOMPLETE accurately. Missing evidence cannot be treated as PASS.
- Preserve the last deployment image and launcher/settings. Promote only the exact tested integration commit, after owner approval. Changing a Git branch does not change the daily server.

The first promotion must replace the bootstrap status with a dated evidence record. Future promotions must include the previous deployment identity and rollback instructions. Keep validated updates fast-forward where possible; stop and plan any history divergence rather than force-pushing.

## Synchronization and permissions

Update `strix-upstream` explicitly to verified community history, then reconcile integration separately. Upstream may rebase; if the tracking update is not a fast-forward, preserve the old pointer and get explicit approval for a lease-protected reset. Do not rewrite integration history to hide tested changes or failures.

No scheduled sync, automatic merging, automatic promotion, deployment, new CI workload or issue/comment publication is configured by this setup. GitHub Actions was disabled in the fork at setup and is left disabled. Further pushes and external posts require the owner's authorization under the local project guide.

## Change ledger

| Candidate | State | Next gate |
| --- | --- | --- |
| [Image-history QSA, Strix PR #163](https://github.com/halo-box/strix-llama.cpp/pull/163) | Integrated from `0a3e66dd2f824925882b2e65986672f4aba1d4cc` in merge `4e2bdbb2920bdb9447ffdb93fc51cb7e028a3e83`; original commits preserved. | Code tree matches the tested candidate outside docs/fork. Existing s50 evidence reused; no fresh GPU tests or daily promotion. |
| Concurrent foreign-image QSA scan crash fix | Separate local candidate; CPU regression and CodeRabbit review available, not integrated by this setup. | Full-model ROCm/Vulkan validation and current overlap check. |
| Selected mainline improvements | None selected or integrated. | Scope, provenance, compatibility and correctness review. |

PR #163 was integrated on 2026-10-10 with David's approval for experimental use after disclosure of its intentional image-selection change. This does not promote it to `validated`, assert image equivalence, or fix the separate concurrent foreign-image full-scan crash. The initial branch table and bootstrap.json are historical setup records; changes.json records subsequent integrations. Revalidate each later code combination before daily promotion.

Keep private workloads, screenshots, model data and raw benchmark artifacts out of the public repository. Store detailed evidence in the local StrixFork campaign workspace, and publish only reviewed summaries.

Setup documentation written with Codex. No performance or global correctness claim is made by branch creation.
