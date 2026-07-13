# Repository Guidelines

## Project Structure & Module Organization

This repository contains standalone Clash/Mihomo classical rule-provider payloads at the repository root:

- `w-ai.yaml` routes AI application processes, domains, telemetry endpoints, IP ranges, and an ASN.
- `w-free.yaml` contains manually curated domains that use the custom free-routing policy.
- `.gitignore` excludes macOS `.DS_Store` files.

There is no application source tree, generated asset directory, or build output. Keep each change scoped to the relevant ruleset; add a new root-level YAML file only when it represents a distinct provider.

## Validation & Development Commands

No build or local server is required. Validate every edit from the repository root:

```sh
ruby -e 'require "yaml"; ARGV.each { |f| p=YAML.load_file(f).fetch("payload"); abort "#{f}: invalid" unless p.is_a?(Array) && p.all?(String) && p.uniq == p; puts "#{f}: #{p.size} rules" }' w-*.yaml
git diff --check
git diff -- w-ai.yaml w-free.yaml
```

The Ruby check verifies valid YAML, a top-level `payload` array, string entries, and no exact duplicates. The Git commands catch whitespace errors and support a final semantic review.

## Style & Naming Conventions

Use two-space YAML indentation and one rule per list item. Write matcher types in uppercase, followed by comma-separated values, for example `DOMAIN-SUFFIX,example.com` or `IP-CIDR,192.0.2.0/24,no-resolve`. Prefer a registrable base domain with `DOMAIN-SUFFIX` when all subdomains should match. Preserve the file's existing matcher style; do not broaden entries to wildcard or keyword matches without a clear need. Group related rules under short comments, keep nearby entries logically ordered, and avoid trailing whitespace.

## Testing Guidelines

There is no automated test framework or coverage target. The validation command above is the required baseline. Also check that new domains are in the intended payload, are not already covered by an existing suffix, and use the narrowest appropriate matcher.

## Commit & Pull Request Guidelines

Recent history uses short imperative subjects such as `Add Postman domain to free rules` and `Update AI routing rules`. Keep commits focused on one ruleset or domain group. Pull requests should explain the routing purpose, list notable domains or processes, identify the target file, and include validation output. Screenshots are unnecessary for data-only changes; link an issue or authoritative domain reference when available.

## Security & Configuration Tips

Commit only public routing matchers. Never add subscription URLs, proxy credentials, API tokens, or local Clash profile data. Review broad `DOMAIN-KEYWORD`, process regex, IP, and ASN rules carefully because they can redirect unrelated traffic.
