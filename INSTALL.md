# Installing SilentGroupCall — step by step

This plugin can't just be dropped into Discord — Vencord custom plugins must be
**compiled into** a Vencord build. Takes ~10 minutes the first time, mostly waiting
on installs.

## What you need first

1. **Git** — https://git-scm.com/download/win (defaults are fine)
2. **Node.js** (LTS) — https://nodejs.org
3. **pnpm** — after installing Node, open a terminal and run:
   ```
   npm i -g pnpm
   ```
4. The plugin file `index.ts` (from this folder).

Open a fresh terminal (PowerShell is fine) after installing these so they're on PATH.

## Part 1 — Get Vencord's source and add the plugin

```powershell
cd $HOME
git clone https://github.com/Vendicated/Vencord
cd Vencord
mkdir src\userplugins\silentGroupCall
copy path\to\index.ts src\userplugins\silentGroupCall\
pnpm install
```

> ⚠️ **Keep the `Vencord` folder permanently.** Discord will load files from it —
> don't delete it after installing. `$HOME` (your user folder) is a good spot;
> avoid Downloads.

## Part 2 — Build and inject

Which path you take depends on whether Vencord is **already installed** (via the
official Vencord installer) or not:

### Path A — you do NOT have Vencord yet (clean install)

```powershell
pnpm build
pnpm inject
```

`pnpm inject` opens a small installer — pick your Discord (usually **Stable**) and
confirm. Done, skip to Part 3.

### Path B — you ALREADY have Vencord via the official installer

Don't use `pnpm inject` — the installer version will fight it and win (its patcher
"self-repairs" the loader on every restart, we learned this the hard way). Instead,
build in **standalone** mode and replace the installed copy's files:

```powershell
pnpm buildStandalone
copy dist\patcher.js  $env:APPDATA\Vencord\dist\
copy dist\preload.js  $env:APPDATA\Vencord\dist\
copy dist\renderer.js $env:APPDATA\Vencord\dist\
copy dist\renderer.css $env:APPDATA\Vencord\dist\
```

> It must be `pnpm buildStandalone`, **not** `pnpm build` — a regular dev build
> silently crashes when copied out of the repo folder and Vencord just won't load.

(Optional but smart: back up `$env:APPDATA\Vencord\dist` to `dist.bak` first, so
you can revert to stock Vencord by copying it back.)

## Part 3 — Restart and enable

1. **Fully quit Discord**: right-click the Discord icon in the **system tray** →
   Quit Discord. (Closing the window or Ctrl+R is NOT enough — the patcher only
   loads on a real restart.)
2. Relaunch Discord.
3. **User Settings → Vencord → Plugins** → search **silent** → toggle
   **SilentGroupCall** on.
4. Click its gear to configure:
   - **Silence group calls** (default on)
   - **Silence DM calls** (default off)
   - **Debug logs** (off; turn on only when troubleshooting)

Test it: start a call in a group DM from a second account's perspective — the call
bar appears but nobody gets rung.

## Updating later (when Discord updates break things)

```powershell
cd $HOME\Vencord
git pull
pnpm install
```

then redo Part 2 (your path) and Part 3 step 1–2. Your plugin stays in
`src\userplugins`, so it's automatically included in every rebuild.

> ⚠️ If you took **Path B**: never click Vencord's built-in **Update** button — it
> overwrites your build with stock Vencord and the plugin disappears (fix: redo
> Part 2).

## Troubleshooting

- **Plugin not in the list** → you probably built before copying `index.ts` in, or
  didn't tray-quit Discord. Rebuild and restart properly.
- **Vencord itself vanished** (Path B) → you used `pnpm build` instead of
  `pnpm buildStandalone`, or your Vencord clone is outdated — run the update steps.
- **Console says `could not find ring/stopRinging module — plugin inactive`** →
  a Discord update changed internals; the plugin needs a code fix, not a rebuild.
- Console access: **Ctrl+Shift+I** → Console tab → filter `SilentGroupCall`.
  A healthy start prints `patched ring()`.

## Note

Client mods violate Discord's ToS on paper; there are no known bans for simply
using them, and this plugin only *skips* a request your client would send — it
never sends anything itself. Still, use judgment on important accounts.
