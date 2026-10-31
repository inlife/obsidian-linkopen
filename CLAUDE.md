# LinkOpen Obsidian plugin

Copies a note, heading or block as an https LinkOpen link (`https://linkopen.app/s/...`) that opens the `obsidian://` link. Listed in Obsidian's community plugins as `linkopen`: https://community.obsidian.md/plugins/linkopen

## Commands

- `npm ci` installs dependencies.
- `npm run build` builds `main.js`. It isn't committed; the release workflow builds it.
- `npm run dev` rebuilds on every change.
- `npx vitest run` runs `test/links.test.js`.

## Rules

- `src/codec.js` is a copy of `utils/codec.js` in github.com/inlife/linkopen. Keep the two identical. Never change its dictionary: existing links depend on it.
- The plugin makes no network requests. Keep it that way, or update the README's Privacy section, which Obsidian's developer policies require.
- Never edit the version in `manifest.json`, `package.json` or `versions.json` by hand. `npm version` updates all three.

## Releasing

Releases are made with git tags, created locally by `npm version`:

1. Make sure `main` is clean and up to date, and the build and tests pass.
2. Run `npm version patch` (or `minor` / `major`). It updates `manifest.json`, `package.json` and `versions.json`, commits, and creates a local tag with the bare version, no `v` (for example `1.0.1`; `.npmrc` sets this).
3. Push the commit and the tag together, naming the tag: `git push --atomic origin main 1.0.1`. With `--atomic`, either both land or neither does, so `main` never has a version without its tag.
4. The push of the tag starts `.github/workflows/release.yml`. It builds `main.js`, checks that the tag matches `manifest.json`, and creates the GitHub release with `main.js` and `manifest.json` attached. If the release already exists, it attaches the files to it instead.
5. Check the release page and the Release run on GitHub. Obsidian offers the update once `main` and the release have the same version.

If that push is refused because of the tag (Claude Code cloud sessions can't push tags), nothing was pushed. Undo the local version commit and tag (`git tag -d 1.0.1 && git reset --hard HEAD~1`, right after `npm version` only), and ask the maintainer to run steps 2 and 3 on their machine. Don't create the tag or the release any other way, such as through the GitHub API or the web UI on their behalf.
