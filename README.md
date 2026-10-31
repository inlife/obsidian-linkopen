# LinkOpen

Copy a link to the current note, heading or block as an https link, so you can paste it into Notion, Slack, GitHub, Gmail and other places that drop `obsidian://` links.

The plugin builds the note's `obsidian://open` link and wraps it in a [LinkOpen](https://linkopen.app) link:

```
obsidian://open?vault=My%20Vault&file=Projects%2FRoadmap%23Q3
https://linkopen.app/s/1kAHFkJ4KQEwAwGoEMCuAbALoeAOAKAnA9gFYCmAxmgM7QBiASnggCYC2CADtAMwCKHQA
```

Clicking the https link opens a page on linkopen.app, which passes the `obsidian://` link on to Obsidian.

## Install

In Obsidian, open **Settings → Community plugins**, select **Browse**, search for "LinkOpen", then install and enable it. You can also [open the plugin's page in Obsidian](https://linkopen.app/1/obsidian%3A%2F%2Fshow-plugin%3Fid%3Dlinkopen) or see it in the [community directory](https://community.obsidian.md/plugins/linkopen).

**By hand**: download `main.js` and `manifest.json` from the [latest release](https://github.com/inlife/obsidian-linkopen/releases/latest) into `<your vault>/.obsidian/plugins/linkopen/`, reload Obsidian, then enable LinkOpen in **Settings → Community plugins**.

## Usage

Commands (open the command palette and type "LinkOpen"):

| Command | Copies |
| --- | --- |
| Copy link to current note | A link to the open note |
| Copy link to current heading or block | A link to the heading or block on the cursor line. Shown only when that line is a heading or ends with a block ID such as `^abc123` |
| Copy Markdown link to current note | `[Note title](https://linkopen.app/s/...)`, whatever the copy format setting says |

The commands have no default hotkeys. You can add your own in **Settings → Hotkeys**.

Menus:

- Right-click a note, for example in the file explorer, and select **Copy LinkOpen link**.
- Right-click in the editor and select **Copy LinkOpen link to note**. On a heading or block line, the menu also has **Copy LinkOpen link to heading** or **Copy LinkOpen link to block**.

A notice confirms each copy.

Block links need a block ID in the note: a space, `^` and an ID made of Latin letters, numbers and dashes at the end of the line, for example ` ^my-id`. For a list, quote, callout or table, the ID goes on a line of its own after it. See [Link to a block](https://obsidian.md/help/links) in Obsidian's help.

### Who can open the link

The link names a vault and a note. It opens the note for someone who has Obsidian installed, a vault with the same name, and the note at the same path, for example a vault you share through Obsidian Sync, iCloud or git. For anyone else, the link can't open the note.

## Settings

- **Open mode**: what happens when someone clicks a copied link.
  - **Open Obsidian right away** (default): the linkopen.app page asks the browser to open Obsidian as soon as it loads.
  - **Show a confirmation page first**: the page shows an **Open link** button.
- **Copy format**: **Plain link** (default) copies the https link. **Markdown link** copies `[Note title](link)`. Links to a heading or block are labelled `[Note title › Heading](link)` or `[Note title › ^block-id](link)`. Square brackets in titles are escaped.

## Privacy

- The plugin makes no network requests. Copying a link only writes to your clipboard.
- A link contains your vault name, the note's path in the vault and any heading or block ID. They are encoded, not encrypted, so anyone who has the link can read them. Don't share links to notes whose names you want to keep private.
- Remote services: the links point to linkopen.app. The plugin never contacts it; opening a link does. That page doesn't load Google Analytics, and Vercel Analytics records it with the link replaced by its app name (for example `/s/obsidian`).

## How the link is built

- The note link follows [Obsidian's URI format](https://obsidian.md/help/Extending+Obsidian/Obsidian+URI): `obsidian://open?vault=<vault>&file=<path without .md>`, with values URI-encoded (spaces as `%20`, slashes as `%2F`).
- A heading goes inside the `file` value after an encoded `#`: `file=Projects%2FRoadmap%23Q3`. A block uses an encoded `#^`: `file=Projects%2FRoadmap%23%5Eabc123`.
- The wrapped link is `https://linkopen.app/s/<data>`, made with the same encoder as the linkopen.app converter (`src/codec.js`). If the compressed form is ever longer, the plugin uses `https://linkopen.app/<type>/<encoded link>` instead, as the site does.

## Development

```bash
npm install
npm run dev     # rebuild main.js on every change
npm run build   # production build
```

To try a build, copy `main.js` and `manifest.json` into `<vault>/.obsidian/plugins/linkopen/`.

- `src/main.js`: commands, menus and copying
- `src/settings.js`: settings and the settings tab
- `src/links.js`: building the links, with no Obsidian imports
- `src/codec.js`: a copy of `utils/codec.js` from the [LinkOpen site](https://github.com/inlife/linkopen). Keep it identical to that file. Its dictionary must never change, because existing links depend on it.

Tests are in `test/links.test.js`. Run them with `npx vitest run`. They decode every link with `src/codec.js` to check it round-trips.

## Releasing

These steps are for the maintainer.

1. Run `npm version 1.0.1` (or the next version). It updates `manifest.json` and `versions.json`, commits, and creates a tag without a `v`.
2. Push the commit and the tag together: `git push --atomic origin main 1.0.1` (with the new version). If you can't push tags, push the commit, then on GitHub use **Releases > Draft a new release**, type the version as a new tag on `main`, and **Publish release**.
3. `.github/workflows/release.yml` builds the plugin and attaches `main.js` and `manifest.json` to that release. It fails if the tag and `manifest.json` disagree. Check the release page once the workflow is done.

Obsidian offers the update once `manifest.json` on `main` has the new version and the release with that version has its files attached.

## License

[MIT](LICENSE)
