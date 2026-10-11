# livebranch adapters

[livebranch](https://livebranch.dev) runs a TypeScript project in the browser, straight from a
GitHub branch, pull request or commit. A plain library with a `src/index.ts` needs nothing. A
project with build steps needs a small config that says how it runs.

That config can live in the project, as `livebranch.json` at its root or in `.github`. An adapter
is the same file kept here, for projects that do not carry one. The project's own file wins over
its adapter, field by field.

## Adding one

Add `adapters/<name>.json`, where `<name>` is the `name` of the project's package.json. A scoped
package goes in a directory, as `adapters/@scope/name.json`. The file names the repository it is for
in its `repository` field. A fork gets the adapter too, as long as its package.json still names the
original repository. For a project that the adapter does not fit by itself, `adapter=<name>` in the
link applies it anyway.

To try a config before sending it here, put it in a link as `livebranch.json`. The command line of
livebranch does that with `livebranch link --gh owner/repo@ref livebranch.json`.

## Fields

| Field | Meaning |
| --- | --- |
| `about` | What the adapter does and why. Only people read it. |
| `repository` | Required in an adapter. The GitHub repository it is for, as `owner/repo`. Package names are not unique across GitHub, so the adapter applies only to that repository and to projects whose package.json names it as their `repository`, which forks do. |
| `entry` | The page to open, or the script to run. |
| `template` | The page for an entry that is a script. `<!--entry-->` marks where the script goes. |
| `setup` | Shell commands that run before the build. `node script.ts` runs the script in the browser, with `fs`, `path`, `glob` and `process` over the project's files. |
| `alias` | Bare import specifiers that point at a project file or a URL. |
| `entries` | Modules that nothing imports statically but the page loads, such as worker scripts. |
| `files` | Extra files, written when the project does not have them. |
| `prebuilt` | Generated files to take from the project's published npm package, for generators that cannot run in a browser. Each has `files` and, as `inputs`, the generator's sources. A warning is reported when those differ from the release's. |
| `cdn` | `esm.sh` (the default) or `jsdelivr`, for imports of npm packages. |

`template` and the values of `files` may be arrays of lines.

An adapter describes the project as it is on its main branch today. A commit from before its build
changed may not run with it. A `livebranch.json` in the project does not have that problem.
