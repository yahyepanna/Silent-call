# Install

You need [Git](https://git-scm.com/download/win), [Node.js](https://nodejs.org) and pnpm (`npm i -g pnpm`).

## 1. Add the plugin to Vencord

```powershell
cd $HOME
git clone https://github.com/Vendicated/Vencord
cd Vencord
mkdir src\userplugins\silentGroupCall
copy path\to\index.tsx src\userplugins\silentGroupCall\
pnpm install
```

Keep the `Vencord` folder; you'll rebuild from it.

## 2. Build and install

**Fully quit Discord first** (system tray → Quit Discord).

**No Vencord yet:**

```powershell
pnpm build
pnpm inject
```

**Vencord already installed via the official installer:**

```powershell
pnpm buildStandalone
Copy-Item -Recurse $env:APPDATA\Vencord\dist $env:APPDATA\Vencord\dist.bak
copy dist\patcher.js, dist\preload.js, dist\renderer.js, dist\renderer.css $env:APPDATA\Vencord\dist\
```

Don't use Vencord's **Update** button afterwards; it replaces your build with stock Vencord.

## 3. Enable

Start Discord → **Settings → Vencord → Plugins** → enable **SilentGroupCall**.

## Updating

```powershell
cd $HOME\Vencord
git pull
pnpm install
```

Then repeat step 2.

## Troubleshooting

Open DevTools (**Ctrl+Shift+I**) → Console and filter for `SilentGroupCall`.

- `intercepting call ring requests`: the plugin is working.
- An error ending in `plugin inactive`: a Discord update broke something. Open an issue.
- To revert to stock Vencord, copy `dist.bak` back over `dist`.
