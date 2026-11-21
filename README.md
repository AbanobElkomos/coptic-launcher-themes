# Coptic Launcher themes

Theme store for [Coptic Launcher](https://github.com/AbanobElkomos). The app reads `index.json` from:

`https://raw.githubusercontent.com/AbanobElkomos/coptic-launcher-themes/main/index.json`

| Folder | Contents |
|---|---|
| `index.json` | the store list the app shows |
| `themes/` | theme packages (`.zip` with `theme.json` + optional `header.webp`) |
| `previews/` | 640×360 preview per theme |
| `src/` | unzipped sources of each package |

## Add or update a theme

1. Put `theme.json` (and `header.webp`) in `src/<id>/`. For an update, raise `version`.
2. Zip the two files (no folder inside) into `themes/<id>.zip`.
3. Add a 640×360 `previews/<id>.webp`.
4. Add or edit the entry in `index.json`: same `id` and `version`, `size` = zip size in bytes.
5. Commit. Units see it the next time they open the store.

Full format: `planning/THEME_STORE.md` in the app project.

Paid themes must **not** be uploaded here: anything in a public repository can be downloaded by anyone.
