# Sending 2.1.1 native release preparation

Recorded 2026-09-16 10:19 Australia/Melbourne; review and cleanup finalized 2026-09-16 10:25.

Status: prepared and tested; merge, annotated tag, Packagist ingestion, and final public-consumer acceptance remain ROOT-owned gates.

## Immutable inputs and scope

- Source: `Sendmux/sendmux-sdk@fb6085ce643c117dc3f2beb29c4fc57c303272b7`, `packages/php/sending/`.
- Source package tree: `18408cfd7dc0d8d71cf93a739a2ca35f49487859`.
- Fresh split base: `Sendmux/sendmux-php-sending@7d0b41e286e99589ab961c1e4edea781a48f5011` (`origin/main`).
- Prepared source commit: `579fb1b2eafb7480b2425479594e3ee7401c5c9f`.
- Release worktree: `/Users/rj/Desktop/GIT-REPOS/sendmux-php-sending-mcp-2-release`, branch `agent/mcp-2-release`, target `main`.
- Intended package/tag: `sendmux/sending` 2.1.1 / `v2.1.1`.
- Four source files changed: `README.md`, `CHANGELOG.md`, `composer.json`, `src/Configuration.php`; 18 insertions, 6 deletions.
- Every one of the 42 package files matches the immutable source. The sole exception replaces `## Unreleased` with `## 2.1.1 (2026-09-16)`.
- The Composer manifest remains version-free. The persistent-cookie upgrade warning and pre-publication installation gate remain intact.
- Existing MAIN `.gitignore`, `.claude/`, and the unrelated `agent/auth-surface` worktree are preserved. The dry-run reaper held auth-surface for unshipped work and removed nothing.

## Correctness trace

`composer.json:11-13` raises the approved floors to Guzzle `^7.15.5`, PSR7 `^2.13.1`, and Core `^2.1.1`. `README.md:19-30` retains the installation gate and the persistent-cookie migration warning; `CHANGELOG.md:3-7` finalizes the approved security note.

`src/Configuration.php:103` changes only the default User-Agent version; `src/Configuration.php:432` changes only the debug package version. The public signatures and algorithms are unchanged. Callers in `src/Api/EmailsApi.php:557`, `src/Api/MetaApi.php:462`, and `src/Api/AttachmentsApi.php:588` read that same configuration value when building request headers; all eight call sites were inspected.

Baseline execution reports User-Agent `OpenAPI-Generator/2.1.0/PHP` and debug package version `2.1.0`. Installed-candidate execution reports `OpenAPI-Generator/2.1.1/PHP` and `2.1.1` through the existing public configuration methods.

Canonical packaging follows immutable `scripts/check-php-splits.mjs:8-87`: path-version fixture, real Composer installation, strict package validation, and platform checks. This bounded check substitutes the already-public Core 2.1.1 for a local Core fixture. No Sendmux request, credential lookup, provider call, or email occurred.

## Installed verification

Environment: PHP 8.4.10, Composer 2.8.9, PHPUnit 11.5.56. The isolated consumer requires exact `sendmux/sending:2.1.1` as a mirrored local candidate and exact public `sendmux/core:2.1.1`. No other Sendmux package is installed and no public dependency source is overridden.

- `composer validate --strict candidate/composer.json`: passed.
- `composer install --no-interaction --no-progress --prefer-dist`: passed with normal public dependency resolution.
- `composer check-platform-reqs`: passed for PHP and every required extension.
- `composer audit --locked --format=json`: passed; zero advisories and zero abandoned packages.
- `php -l src/Configuration.php`: passed.
- `vendor/bin/phpunit --no-configuration --bootstrap vendor/autoload.php --do-not-cache-result tests`: **22 tests, 59 assertions, zero skipped**.
- The unchanged test selection is `CoreTest`, `OAuthRetryTest`, `AttachmentUnionTest`, and `ConnectionResponseTest`; all four tests and six support files match the immutable source byte-for-byte.
- Installed diagnostics resolve Configuration and Core Auth classes from this consumer's `vendor/` paths, not a monorepo checkout.
- All 42 installed Sending files equal the candidate and approved source, with the same changelog-only exception.
- Public Core `v2.1.1` was installed as a ZIP from `https://github.com/Sendmux/sendmux-php-core.git` at `42769909a6c2fb34fa11657c8dce7086c00e9c25`; live Packagist independently returned that exact version/reference.
- Resolved Guzzle is `7.15.5`; PSR7 is `2.13.1`.

Tests: +none / -none. This split prepares already-reviewed immutable code; it adds no speculative behavior or duplicate regression tests. The installed checks cover auth rejection, headers, pagination, error mapping, retry limits, attachment union serialization, and invalid async connection responses. Browser journeys: not applicable to this PHP package preparation; final public native acceptance remains pending.

One preparation check exited 1: strict validation of the temporary consumer warns about its intentional exact version pins. The package candidate passed strict validation, and ordinary consumer validation exited 0 with those same pin warnings. This preserves the required exact-version acceptance target; no dependency bounds or behavior were weakened. An initial test archive used four stripped components, producing an empty directory; correcting the three-directory archive prefix restored the exact ten files before any test execution.

## Evidence receipts

Bulk outputs are in MAIN `.claude/artifacts/mcp-2-release/`; this worktree links that same artifact directory. `parity-and-provenance.json` records every source/release/installed SHA-256, all ten immutable test hashes, source tree, base, and dependency origins. `verification-inputs/` preserves the consumer manifest, lockfile, diagnostics, and unchanged tests.

| Receipt | SHA-256 |
| --- | --- |
| `phpunit.log` | `4e8310b0d5fe729638657cab20ed6446b732a29aa72e4f015008be674c751f6d` |
| `diagnostics.log` | `dc4048487540e5b5b93853ea2a4d5c04a9749d54c4465c36eec27da8d623b1fd` |
| `audit.log` | `a35714bd7ae535da6f7fd648edb57f52763794ae1cfdf7fb394d0e67bdaec845` |
| `parity-and-provenance.json` | `d634cc3bf084c2ec75f46fe05b897ae48a486e4c646c2869a00bde10daaa6ad4` |
| `process-receipts.jsonl` | `87e3a3d5be854b6f34f527c87465c3b06def9576051b6c13b84c5290f66ea1da` |

Additional receipts: `consumer-install.log`, `candidate-validate.log`, `platform.log`, `installed-provenance.log`, `baseline-diagnostics.log`, `configuration-lint.log`, `packagist-core.json`, `packagist-sending-before.json`, `tag-before.log`, and `cleanup.json`. Live preflight confirmed `v2.1.1` absent from Sending tags and Packagist. The preserved consumer lockfile SHA-256 is `c5146accad110fcbefbd93a3e722f30e8e09414c3ead5eb669bc3ee2bdcae199`.

## Review and handoff

Independent pre-push review passed with no P1/P2 findings. The reviewer independently reconciled all 42 source files, all ten test files, receipt hashes, dependency provenance, and cleanup; fresh public-method execution also verified the custom User-Agent override remains effective. The source commit was unchanged after review. The retained `sending-native-review.md` artifact has SHA-256 `56a8a79435a4fd6fdc8665f9f71e37d0d5a38e653cf884bff9155612cf90163c`.

The reviewer confirmed this diff is non-major under the repository's actual-diff rule: the only source-code edits are two version literals; there is no new branching, API signature, auth behavior, schema, refactor, or rollout policy.

Skills applied: `technical-documentation` preserved the version-specific upgrade warning and existing changelog style; `software-design-philosophy` confirmed no added interface complexity; `requesting-code-review` requires the independent bounded review; `writing-for-agents` scopes its brief. No extra source refactor or documentation rewrite was introduced.

## Completion gates and resources

- Prepared/tested/reviewed: exact source synchronization, package validation, installed Core/Sending tests, diagnostics, platform, audit, provenance and independent pre-push review.
- Pending after this evidence commit: ready PR, live PR settlement, ROOT merge/tag/publication, exact public Sending version/reference, and ROOT's public consumer acceptance.
- Processes: all nine recorded Composer/PHP/PHPUnit subprocesses exited and their exact PIDs returned ESRCH; orchestrator session 75753 exited 0. No server, browser, container, tunnel or seeded data was created.
- Temporary consumer `MAIN/.claude/artifacts/mcp-2-release/consumer.gfDlQB` and materialized source `MAIN/.claude/artifacts/mcp-2-release/source` were removed and verified absent. Preserved verification inputs and hash receipts remain as evidence; `cleanup.json` records the exact targets.
- Worktree: retained for the PR, awaiting ROOT merge; no teardown claim is made.
- Parked: none.
