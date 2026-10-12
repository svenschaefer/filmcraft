# Quality checks

The general CI workflow checks every pull request and push to main on Linux and Windows
with Rust 1.95.0. It runs the repository's complete gate:

```sh
cargo xtask ci
```

The gate checks formatting, release-profile Clippy, release-profile workspace tests,
dependency layering, asset attribution and WebAssembly compilation.

CI installs ffmpeg and ffprobe, verifies both executables and sets
FILMCRAFT_REQUIRE_ORACLES=1. Missing reference tools therefore fail the tests instead of
silently reducing coverage. These tools generate fixtures and verify results; they are
not linked into or shipped with FilmCraft. See [Testing](testing.md) for the fixture and
codec comparison contracts.

Legacy codec unit fixtures use the shared PATH/environment-aware tool discovery
and atomic fixture generator. Required-oracle mode also rejects fixture-generation,
reference-read and VP9 first-pass failures instead of silently skipping those checks.

Windows installs `ffmpeg-full`, and CI verifies its `libdav1d` decoder before testing
AV1 fixtures. The [build publisher](https://www.gyan.dev/ffmpeg/builds/) lists the
available packages.

DNxHD/DNxHR fixture generation limits the reference encoder to two threads so UHD
fixtures do not allocate a frame-thread pool proportional to the host CPU count.
All profiles, resolutions and pixel-comparison tolerances remain unchanged.

Caption-font automation registers the visible portion of each scrollable menu row.
The headless font-picker regression still selects the bundled Noto Serif family
and checks that private system faces are excluded.

The macOS microphone-permission test normalizes checked-out XML line endings
before checking the usage key and enabled audio-input entitlement. The package
files and permission assertions are unchanged.

Media Foundation regressions capture backend and fallback diagnostics. The parity
checks still require matching pixels and continued hardware decoding when a
hardware decoder is available.

Linux uses the pinned first-party craft-fonts input; Windows exercises the optional-font
fallback build. Neither platform job is allowed to hide a failing test behind the other.

The aggregate check is named **quality gate**. Enable it in the main branch rules after
the workflow has run; the workflow file itself does not configure branch protection.
