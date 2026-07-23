# herdr


<p align="center">
  <img src="assets/logo.png" alt="herdr" width="100" />
</p>

<p align="center">
  <a href="https://herdr.dev">herdr.dev</a> · <a href="#install">install</a> · <a href="https://herdr.dev/docs/quick-start/">quick start</a> · <a href="https://herdr.dev/docs/">docs</a> · <a href="#sponsors">sponsors</a>
</p>

<p align="center">
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-Apache--2.0-666666?labelColor=333333" alt="Apache 2.0 license" /></a>
  <a href="https://github.com/ogulcancelik/herdr/releases"><img src="https://img.shields.io/github/downloads/ogulcancelik/herdr/total?labelColor=333333&color=666666" alt="total GitHub release downloads" /></a>
  <a href="https://github.com/ogulcancelik/herdr/stargazers"><img src="https://img.shields.io/github/stars/ogulcancelik/herdr?labelColor=333333&color=666666&logo=github" alt="GitHub stars" /></a>
  <a href="https://github.com/ogulcancelik/herdr/releases/latest"><img src="https://img.shields.io/github/v/release/ogulcancelik/herdr?label=release&labelColor=333333&color=666666" alt="latest stable release" /></a>
  <a href="https://formulae.brew.sh/formula/herdr"><img src="https://img.shields.io/homebrew/v/herdr?label=homebrew&labelColor=333333&color=666666" alt="Homebrew version" /></a>
  <a href="https://x.com/herdrdev"><img src="https://img.shields.io/badge/follow-%40herdrdev-000000?logo=x&logoColor=white" alt="follow @herdrdev on X" /></a>
</p>

---

> **This fork ([sj671/herdr](https://github.com/sj671/herdr)) adds native synchronized input** — a tmux-style `synchronize-panes` toggle built into the core. See [synchronized input](#synchronized-input-fork-feature) below. Everything else matches upstream [ogulcancelik/herdr](https://github.com/ogulcancelik/herdr).

## synchronized input (fork feature)

Mirror everything you type in the focused pane to every other pane in the same tab — run the same command on several servers, drive several shells at once.

**Toggle**: `prefix+shift+y` (default prefix is `Ctrl-b`, so `Ctrl-b Shift-Y`). A toast confirms *sync input on* / *sync input off*. The binding is configurable:

```toml
[keys]
sync_input = "prefix+shift+y"
```

**How it works**: while sync is on, each keystroke and paste sent to the focused pane is also encoded per-pane (respecting each pane's keyboard protocol) and written to every sibling pane's PTY in the focused tab. Because input is duplicated at the source rather than scraped from screen output, everything works:

- control keys (`Ctrl-C`, `Ctrl-D`, `Ctrl-Z`), arrows, `Tab` completion, `Esc`
- passwords and other non-echoed input
- interactive programs (`vim`, `less`, REPLs)
- pastes, including bracketed paste

**Scope and behavior**:

- Mirroring applies only to panes in the *focused tab*. Other tabs and workspaces are untouched.
- Herdr keybindings (prefix chords, pane navigation, etc.) are intercepted before mirroring, so only input that actually reaches the focused pane is duplicated.
- Popup panes are excluded.
- The toggle is session-global and follows the focused tab; it is not per-tab state and does not persist across server restarts (always starts off).

This replaces the [herdr-synchronize-input](https://github.com/forteleaf/herdr-synchronize-input) plugin, which watches screen output and therefore cannot mirror control keys, passwords, or interactive programs. Uninstall that plugin before using this fork so the two do not double-send, and remove its `prefix+shift+y` `[[keys.command]]` block from `~/.config/herdr/config.toml` so it does not shadow the native binding.

### installing this fork

**Prebuilt binaries** (recommended): download the binary for your platform from the [fork releases page](https://github.com/sj671/herdr/releases), then:

```bash
chmod +x herdr-macos-aarch64        # or your platform's binary
mv herdr-macos-aarch64 ~/.local/bin/herdr
```

macOS may quarantine the download; if it refuses to run, clear it with `xattr -d com.apple.quarantine ~/.local/bin/herdr`.

**From source**: requires a Rust toolchain (1.85+) and Zig 0.15.2 for the vendored libghostty-vt (set `ZIG=/path/to/zig-0.15.2/zig` if it is not on `PATH`):

```bash
git clone https://github.com/sj671/herdr
cd herdr
cargo build --release
cp target/release/herdr ~/.local/bin/herdr
```

**Caveats for existing herdr users**:

- If you have a herdr server running from an older build, the new CLI will refuse to talk to it until you restart the server (it prints the exact stop command). Stopping the server exits pane processes, so finish work in your panes first.
- **Do not run `herdr update`** — it will replace this fork with the stock upstream release. If that happens, just reinstall the fork binary.
- Uninstall the `herdr-synchronize-input` plugin if you have it (`herdr plugin uninstall herdr-synchronize-input`) and remove its `[[keys.command]]` block from `~/.config/herdr/config.toml`, or it will shadow the native `prefix+shift+y` binding.

Implementation lives on the [`sync-input`](https://github.com/sj671/herdr/tree/sync-input) branch: `keys.sync_input` config, `AppState.sync_input` flag, and key/paste fan-out in `src/app/input/terminal.rs` and `src/app/input/mod.rs`.

---

https://github.com/user-attachments/assets/043ec09f-4bdd-41d5-aee0-8fda6b83e267

**agent multiplexer that lives in your terminal.**

- **every agent at a glance** — blocked, working, done. real terminal views, not a wrapped interpretation.
- **detach, agents keep running** — reattach from any terminal, or over ssh. sessions survive restarts.
- **agents can use herdr too** — a pure socket api: agents spawn panes, read output, wait on each other. [agent skill →](https://herdr.dev/docs/agent-skill/)
- **keyboard and mouse, both first-class** — tmux-style prefix keys *and* click, drag, split. pick per moment, not per tool.
- **plugins** — extend panes and workflows. [browse the marketplace →](https://herdr.dev/plugins/)
- **one rust binary, no electron** — runs in whatever terminal you already use.

---

## install

```bash
curl -fsSL https://herdr.dev/install.sh | sh
```

or `brew install herdr` · `mise use -g herdr` · windows beta: `powershell -ExecutionPolicy Bypass -c "irm https://herdr.dev/install.ps1 | iex"` · [binaries](https://github.com/ogulcancelik/herdr/releases)

then start it where the work lives:

```bash
herdr
```

run your agents, split panes, walk away. `ctrl+b q` detaches, `herdr` reattaches. [quick start →](https://herdr.dev/docs/quick-start/)

## docs

everything lives at [herdr.dev/docs](https://herdr.dev/docs/): [quick start](https://herdr.dev/docs/quick-start/) · [concepts](https://herdr.dev/docs/concepts/) · [supported agents](https://herdr.dev/docs/agents/) · [keyboard](https://herdr.dev/docs/keyboard/) · [configuration](https://herdr.dev/docs/configuration/) · [session state](https://herdr.dev/docs/session-state/) · [remote](https://herdr.dev/docs/persistence-remote/) · [integrations](https://herdr.dev/docs/integrations/) · [plugins](https://herdr.dev/docs/plugins/) · [socket api](https://herdr.dev/docs/socket-api/)

## sponsors

herdr is built full-time, in the open. sponsoring directly funds development, stability, and the path to a real agent runtime.

### gold

<a href="https://terminaltrove.com/"><img src="assets/sponsors/terminal-trove.png" alt="Terminal Trove" width="200" /></a>

[**→ become a sponsor**](https://github.com/sponsors/ogulcancelik) · enterprise / partnership: hey@herdr.dev · see [SPONSORS.md](./SPONSORS.md) for tiers. thank you 🐑

## agent instructions

if you are an ai agent helping with this repository, read [`AGENTS.md`](./AGENTS.md) before making changes and read [`CONTRIBUTING.md`](./CONTRIBUTING.md) before opening issues or PRs.

## development

```bash
git clone https://github.com/ogulcancelik/herdr
cd herdr
cargo build --release

just test        # unit tests
just check       # formatting, tests, and maintenance checks
```

## license

Herdr is licensed under the [Apache License 2.0](LICENSE).
