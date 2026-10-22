# Fragment Sample Plugin

A starter plugin for [Fragment](https://github.com/RebornFlamme/Fragment), an
Obsidian-style knowledge app. It mirrors the official
[`obsidian-sample-plugin`](https://github.com/obsidianmd/obsidian-sample-plugin)
but targets Fragment's public type contract,
[`@usefragment/core`](https://www.npmjs.com/package/@usefragment/core).

## What it demonstrates

| Feature | API used |
| --- | --- |
| A ribbon icon that shows a notice and counts clicks | `Plugin.addRibbonIcon`, `Notice` |
| A command that opens a modal | `Plugin.addCommand` (`callback`), `Modal` |
| An editor command (wrap selection in `**bold**`) | `Plugin.addCommand` (`editorCallback`), `Editor` |
| A conditional command that hides when the view is open | `Plugin.addCommand` (`checkCallback`) |
| A custom side view | `Plugin.registerView`, `ItemView`, `setIcon` |
| A global DOM listener, auto-removed on unload | `Component.registerDomEvent` |
| A repeating interval, auto-cleared on unload | `Component.registerInterval` |
| Persisted settings | `Plugin.loadData` / `Plugin.saveData` |
| A settings tab, declared as data | `static settingDefinitions`, `onExternalSettingsChange` |

## How Fragment plugins work

`@usefragment/core` is a **types-only** package — it ships no JavaScript. At
runtime the Fragment host injects the real implementation through a require shim
(just like the `obsidian` package). Your code imports from the virtual
`fragment` module (the alias `obsidian` also works), and the bundler marks it as
**external** so it is resolved by the host, not bundled in.

```ts
import { Plugin, Notice } from "fragment";

export default class MyPlugin extends Plugin {
  async onload() {
    this.addCommand({ id: "hello", name: "Say hello", callback: () => new Notice("👋") });
  }
}
```

### What the contract covers

`@usefragment/core` 0.3 follows Obsidian's plugin API (commands, views, ribbon,
status bar, modals, menus, `Setting` and settings tabs, `requestUrl`, vault and
workspace events) and adds Fragment's own surfaces: view layers and widgets
(`registerLayer`, `WidgetLayer`), artefacts (`app.artefacts`), document providers,
open registries (`defineRegistry`) and screen capture (`app.windowHost`).

Obsidian's DOM sugar (`el.createEl`, `el.addClass`, `el.empty`, `el.setText`) is
not provided: use standard DOM APIs (`document.createElement`, `el.classList`,
`el.replaceChildren`, `el.textContent`).

## Getting started

```bash
npm install        # install dev dependencies
npm run dev        # bundle src/main.ts -> main.js and watch for changes
npm run build      # type-check, then produce a minified production main.js
```

The plugin's loadable files are `main.js`, `manifest.json` and `styles.css`.
Place this folder in your vault's plugins directory (it already lives under
`.fragment/plugins/`), then enable **Sample Plugin** in Fragment.

## Releasing

1. `npm version patch|minor|major` runs `version-bump.mjs`, which writes the new
   version into `manifest.json` and records the compatible `minAppVersion` in
   `versions.json`. It also creates a git tag equal to the version (no `v`
   prefix, see `.npmrc`).
2. `git push --follow-tags`. The `Release Fragment plugin` workflow builds the
   plugin and publishes a GitHub release with `main.js`, `manifest.json` and
   `styles.css` — the files Fragment downloads.

## Publishing to the community catalog

Open a pull request on
[`RebornFlamme/fragment-releases`](https://github.com/RebornFlamme/fragment-releases)
adding your plugin at the end of `community-plugins.json`. Once merged, it shows
up in **Settings → Community plugins → Browse**.

This repository is a GitHub template: click **Use this template** to start a
new plugin.

## Project layout

```
manifest.json        Plugin metadata (id, name, version, minAppVersion, …)
package.json         Scripts and dev dependencies
tsconfig.json        Loads @usefragment/core via "types"
esbuild.config.mjs   Bundles to main.js, marks `fragment` external
src/main.ts          The plugin
styles.css           Plugin styles (prefer theme CSS variables)
versions.json        plugin version -> minimum Fragment version
version-bump.mjs      Release helper
```

## License

MIT
