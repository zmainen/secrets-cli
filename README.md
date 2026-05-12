# secrets-cli

A CLI tool to manage, lint, and beautify sourced shell secrets files (`~/.secrets`, `.env`, or any `export`-based file).

## Install

```bash
# clone and symlink
git clone https://github.com/zmainen/secrets-cli.git
ln -sf "$(pwd)/secrets-cli/secrets" /usr/local/bin/secrets

# or just alias it
alias secrets="python3 /path/to/secrets-cli/secrets"
```

Requires Python 3.9+ and [Click](https://click.palletsprojects.com/) (`pip install click`).

## Usage

```
secrets                      # preview cleaned + sorted output
secrets apply                # write (creates timestamped .bak)
secrets diff                 # unified diff of what would change
secrets lint                 # syntax errors, bad quoting, bare assigns
secrets list                 # groups + variable names (no values)
secrets grep <pattern>       # find variables by name substring
secrets set KEY "value"      # add or update a secret
secrets rm KEY               # remove a secret
secrets dupes                # show duplicates and value conflicts
secrets dupes --fix          # resolve interactively
secrets check                # exit 1 if file needs cleaning
secrets -f .env lint         # works on any export-based file
```

## What it does

**`apply`** groups exports by prefix, sorts groups alphabetically, aligns `=` signs within each group, deduplicates (last value wins), and normalizes blank lines. Always creates a timestamped backup before writing.

**`lint`** catches: missing `export` keyword (bare assignments), malformed exports, unmatched quotes, unquoted values with spaces, empty values, `unset` lines, duplicate and conflicting exports, and unrecognised lines.

**`set`** adds a new export or updates an existing one. Values are double-quoted by default (`--no-quote` to skip). New exports are inserted next to others sharing the same prefix.

**`rm`** removes exports, bare assignments, and `unset` lines by name. Accepts multiple names. Use `--dry-run` to preview.

**`dupes`** reports exact duplicates and value conflicts separately. With `--fix`, exact duplicates are resolved automatically (keep last); conflicts prompt interactively.

**`grep`** searches variable names (not values). Annotates bare assignments and `unset` lines in the output.

## Safety

- Every mutation (`apply`, `set`, `rm`, `dupes --fix`) creates a timestamped `.bak` with `chmod 600` before writing.
- No command ever prints secret values. `list` and `grep` show names only.
- The tool never reads values into structured output — it operates on lines.

## License

MIT
