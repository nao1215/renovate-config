# renovate-config

Shared [Renovate](https://docs.renovatebot.com/) preset for nao1215 repositories.

## Usage

Put this `renovate.json` at the root of a repository:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>nao1215/renovate-config"]
}
```

## Settings

- `config:recommended`: Renovate's default set. It also skips `**/examples/**` and `**/test/**`, so example projects inside a repository are not updated.
- `rangeStrategy: widen`: most repositories here are libraries published to Hex. Widening keeps the old lower bound and raises only the upper bound (`>= 6.0.0 and < 7.0.0` becomes `>= 6.0.0 and < 8.0.0`), so users are not forced onto the newest major version.
- `lockFileMaintenance`: refreshes `manifest.toml` every week so CI tests the same dependency versions that users resolve. Without it, updates that stay inside the declared range never show up as a pull request.
- GitHub Actions updates are grouped into one pull request.
- Runtime and toolchain versions are not updated: the `node-version` of `actions/setup-node` and every tool in `.mise.toml` (Gleam, Erlang, Node.js). These repositories are libraries, so they do not need to run on the newest runtime. CI lanes pin a floor version on purpose, and widening `"22"` to `"22 || 24"` would silently move the floor lane to Node.js 24. `.mise.toml` follows the CI latest lane and is updated by hand in the same pull request that changes CI.
- What `erlef/setup-beam` installs (`gleam-version`, `otp-version`, `rebar3-version`) is not updated either. The floor lane pins the Gleam version declared in `gleam.toml` (`gleam = ">= 1.14.0"`) and the oldest maintained OTP, and release jobs build the escript on that OTP so it runs on every OTP the README supports. Bumping them would stop CI from checking the declared floor. The latest lane floats on `1.x` and needs no update.
- atago and himorime always follow their latest release. A regex manager reads the `version:` input given to `nao1215/setup-atago` and `nao1215/setup-himorime` in `.github/workflows/*.yml` and opens one grouped pull request when a new release is published. CI keeps a fixed version so a commit always runs the same tools; this pull request moves it forward.
- `timezone: Asia/Tokyo`: schedules such as lock file maintenance run on Monday morning in JST.
