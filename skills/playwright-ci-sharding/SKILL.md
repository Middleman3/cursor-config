---
name: playwright-ci-sharding
description: Playwright sharding, blob reporter, merge-reports HTML artifact in GitHub Actions. Use when scaling E2E CI or debugging shard failures.
---

# Playwright CI sharding

## Pattern

1. **Matrix:** `shard: [1, 2, …, N]` with `strategy.job-total` or fixed denominator matching shard count.
2. **Run:**  
   `pnpm exec playwright test --shard=${{ matrix.shard }}/${{ strategy.job-total }} --reporter=blob`
3. **Artifacts:** Upload `blob-report/` per shard with distinct names (e.g. `playwright-blob-1`).
4. **Merge job:** `needs: [playwright]`, `if: always()`, download `pattern: playwright-blob-*`, `merge-multiple: true`, then:  
   `pnpm exec playwright merge-reports --reporter=html ./all-blob-reports`
5. Upload `playwright-report/` as HTML artifact.

## Config

- In CI, set `reporter` to include `blob` (or rely on CLI `--reporter=blob`).
- Install browsers: `pnpm exec playwright install --with-deps chromium` for speed.

## Tuning

- Increase shards until each shard is ~2–3 minutes; each shard is a separate billed runner.

## Placement

- E2E stays **only in GitHub Actions** when using single-placement local gates (not duplicated in Husky).
