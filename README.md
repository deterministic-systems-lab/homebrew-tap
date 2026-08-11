# GurdyAI/homebrew-tap

The Homebrew tap for [Gurdy](https://github.com/GurdyAI/gurdy) — a flight recorder for AI agents.
It governs agent tool calls and writes a decision ledger a third party can verify offline.

```sh
brew install GurdyAI/tap/gurdy
```

That installs two binaries: `gurdy-proxy` (the governance proxy) and `gurdy-verify` (the offline
ledger verifier). The verifier is deliberately a separate binary — verifying an export must need
nothing but the export and the checker.

## This repository is generated

Nothing here is written by hand. `Casks/gurdy.rb` is published by GoReleaser from the
[`gurdy`](https://github.com/GurdyAI/gurdy) release workflow on every tagged release, and any
edit made here is overwritten by the next one. **Report problems against `gurdy`, not against
this tap** — the cask's contents come from `.goreleaser.yaml` there.

## Why a cask, and why it runs `xattr`

GoReleaser deprecated formulae (`brews`) in favour of casks. That is not a cosmetic move for this
project: Homebrew **formulae are exempt from Gatekeeper and casks are not**. Gurdy ships unsigned
by Apple (there is no Apple Developer account behind it — a deliberate call, recorded in the
roadmap), so a cask installing these binaries would leave `com.apple.quarantine` set and macOS
would greet a first-time user with *"cannot be opened because the developer cannot be verified"* —
on a security tool, which is close to the worst available first impression.

The cask therefore carries a post-install hook clearing that attribute on both binaries. It is
restoring the behaviour formulae get for free, not bypassing a check that would otherwise protect
you.

**What actually attests these binaries** is stronger than notarization and does not depend on
this tap: every release is signed with [cosign](https://github.com/sigstore/cosign) keyless via
GitHub OIDC — no private key exists to be lost or compelled — with each signature logged in
[Rekor](https://rekor.sigstore.dev), a syft SBOM per archive, and a `verify-reproducible` CI job
that rebuilds every tag on a different runner in a different directory and fails the release on a
single differing byte. Verify a download against those rather than against Gatekeeper.

## Other install channels

`brew` is the documented macOS path. Also available: `npm i -g @gurdy/cli`, `pipx install gurdy`,
and an `install.sh` that checks the SHA-256 against `checksums.txt` and refuses on mismatch or on
an archive missing from it.

## Licence

Gurdy is Apache-2.0. This tap holds generated packaging metadata only.
