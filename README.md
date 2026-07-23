# herdr


<p align="center">
  <img src="assets/logo.png" alt="herdr" width="100" />
</p>

<p align="center">
  <a href="https://herdr.dev">herdr.dev</a> · <a href="#install">install</a> · <a href="https://herdr.dev/docs/quick-start/">quick start</a> · <a href="https://herdr.dev/docs/">docs</a>
</p>

<p align="center">
  English · <a href="README.zh-CN.md">简体中文</a>
</p>

<p align="center">
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-Apache--2.0-666666?labelColor=333333" alt="Apache 2.0 license" /></a>
  <a href="https://github.com/herdrdev/herdr/releases"><img src="https://img.shields.io/github/downloads/herdrdev/herdr/total?labelColor=333333&color=666666" alt="total GitHub release downloads" /></a>
  <a href="https://github.com/herdrdev/herdr/stargazers"><img src="https://img.shields.io/github/stars/herdrdev/herdr?labelColor=333333&color=666666&logo=github" alt="GitHub stars" /></a>
  <a href="https://github.com/herdrdev/herdr/releases/latest"><img src="https://img.shields.io/github/v/release/herdrdev/herdr?label=release&labelColor=333333&color=666666" alt="latest stable release" /></a>
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

**Building this fork**: requires Zig 0.15.2 for the vendored libghostty-vt (set `ZIG=/path/to/zig-0.15.2/zig` if it is not on `PATH`), then `cargo build --release`. Implementation lives on the [`sync-input`](https://github.com/sj671/herdr/tree/sync-input) branch: `keys.sync_input` config, `AppState.sync_input` flag, and key/paste fan-out in `src/app/input/terminal.rs` and `src/app/input/mod.rs`.

---

https://github.com/user-attachments/assets/043ec09f-4bdd-41d5-aee0-8fda6b83e267

**the runtime your coding agents live on.**

- **detach without stopping work** — herdr keeps terminals running in a background server when you close the client or lose your SSH connection. after a server or machine restart, herdr restores the saved layout and can resume supported agent sessions; the original processes do not survive. [session state →](https://herdr.dev/docs/session-state/)
- **several machines, one window** — keep local work and saved ssh machines together, with a combined agent list and independent reconnects. [remote machines →](https://herdr.dev/docs/connecting-machines/)
- **never hunt for the stuck one** — every pane is marked working, blocked, or idle. when an agent stops and needs an answer, herdr says so.
- **agent-native** — agents drive herdr through the cli and socket api: they can spawn panes, prompt each other, and wait until another agent is genuinely blocked. [agent skill →](https://herdr.dev/docs/agent-skill/)
- **runs what you already run** — claude code, codex, cursor, opencode, grok and the rest. herdr doesn't wrap or replace them; it owns their terminals.
- **keyboard and mouse, both first-class** — tmux-style prefix keys *and* click, drag, split. pick per moment, not per tool.
- **plugins** — extend panes and workflows. [browse the marketplace →](https://herdr.dev/plugins/)
- **one rust binary, no electron** — runs in whatever terminal you already use.

---

## install

```bash
curl -fsSL https://herdr.dev/install.sh | sh
```

or `brew install herdr` · `mise use -g herdr` · windows: `powershell -ExecutionPolicy Bypass -c "irm https://herdr.dev/install.ps1 | iex"` · [endpoint-protected Windows](https://herdr.dev/docs/windows-beta/) · [binaries](https://github.com/herdrdev/herdr/releases)

then start it where the work lives:

```bash
herdr
```

run your agents, split panes, walk away. `ctrl+b q` detaches, `herdr` reattaches. [quick start →](https://herdr.dev/docs/quick-start/)

## docs

everything lives at [herdr.dev/docs](https://herdr.dev/docs/): [quick start](https://herdr.dev/docs/quick-start/) · [concepts](https://herdr.dev/docs/concepts/) · [supported agents](https://herdr.dev/docs/agents/) · [keyboard](https://herdr.dev/docs/keyboard/) · [configuration](https://herdr.dev/docs/configuration/) · [session state](https://herdr.dev/docs/session-state/) · [connecting machines](https://herdr.dev/docs/connecting-machines/) · [remote](https://herdr.dev/docs/persistence-remote/) · [integrations](https://herdr.dev/docs/integrations/) · [plugins](https://herdr.dev/docs/plugins/) · [socket api](https://herdr.dev/docs/socket-api/)

## thanks

every past sponsor and backer is listed in [SPONSORS.md](./SPONSORS.md) — thank you 🐑

enterprise / partnership: hey@herdr.dev

## agent instructions

if you are an ai agent helping with this repository, read [`AGENTS.md`](./AGENTS.md) before making changes and read [`CONTRIBUTING.md`](./CONTRIBUTING.md) before opening issues or PRs.

## development

```bash
git clone https://github.com/herdrdev/herdr
cd herdr
cargo build --release

just test        # unit tests
just check       # formatting, tests, and maintenance checks
```

## license

Herdr is licensed under the [Apache License 2.0](LICENSE).
