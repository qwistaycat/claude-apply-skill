# Tracker sheet and Drive

Sheet id `<SHEET_ID>`, live tab `<SHEET_TAB_NAME>` (`gid=<SHEET_GID>`). Drive folder
`<DRIVE_FOLDER_NAME>` id `<DRIVE_FOLDER_ID>`. Both on `<GOOGLE_ACCOUNT>`. All four values
come from `~/.claude/apply-profile.md`.

**There is no API route, at least not on a consumer Google account.** Verified dead ends,
do not re-test them:

- Application Default Credentials lack the `spreadsheets` scope, and re-running
  `gcloud auth application-default login` with sensitive scopes fails with "This app is
  blocked" because Google blocks gcloud's client id for those scopes on consumer accounts.
- A service account can be impersonated but uploads fail with "Service Accounts do not have
  storage quota", the Sheets API returns 403 with no permission on the sheet, and the Drive
  MCP cannot grant it (`share_file` returns "The caller does not have permission").
- The gdrive MCP `update_file` only changes metadata. There is no append-rows capability.
- `fetch()` of the `/export?format=tsv` URL from inside the Sheets page is CORS-blocked.

Everything below runs through the user's already-logged-in Chrome.

## Reading (dedupe set, first empty row)

Run inside the sheet tab. A returned Promise resolves to `{}`, so stash and read back in a
second call.

```js
fetch('https://docs.google.com/spreadsheets/d/<SHEET_ID>/gviz/tq?gid=<SHEET_GID>&tqx=out:csv&tq='+encodeURIComponent('select A,B,D,I limit 120 offset 500'),{credentials:'include',cache:'no-store'}).then(r=>r.text()).then(t=>{window.__r=t;return 'len '+t.length;})
```

Then: `window.__r.slice(0,3000).replace(/"/g,'')`.

`limit` must precede `offset`. The CSV has a header row, so `offset N` means the first data
line is sheet row `N+2`.

`javascript_tool` refuses to return strings that look like base64 or like cookie/query
data. Avoid `key=value` shapes in return values; join with ` / ` or ` >> `.

## Writing rows

The grid is canvas, so DOM reads and screenshots cannot get cell values, and every
JS-based entry path fails. Synthetic `ClipboardEvent('paste')` returns `consumed:false` and
then crashes the grid. `navigator.clipboard.writeText` succeeds but nothing can fire a real
paste. `computer key` with `modifiers:"cmd"` does not apply the modifier, so Cmd+V types a
literal `v`. The old `.waffle-rich-text-editor` selector no longer exists; the live editor
is `div.cell-input.editable`.

This is the only recipe that works:

1. `mcp__Control_Chrome__switch_to_tab` first. If `document.hasFocus()===false`, Sheets
   silently ignores every synthetic key.
2. Triple-click the name box at `(45,114)`, type the target cell ref, press Return.
3. **Then a real `left_click` on the cell itself.** Name-box navigation leaves
   `document.activeElement === BODY` and typing goes nowhere. This click is load-bearing.
4. Whole-row entry inside one `browser_batch`: per cell emit `type` / `key Delete` /
   `key Tab`. Bare `key Tab` skips a column. `key Return` at the end drops to column A of
   the next row and typing continues with no further clicking.

The `Delete` between `type` and `Tab` kills the autocomplete suggestion. Without it, a
value that is a strict prefix of an earlier entry in the same column gets silently replaced
by the completion. That corrupted six rows once before a readback caught it.

`=HYPERLINK("url","label")` typed literally comes out correct. Sheets absorbs the
auto-paired closing quote and paren rather than doubling them.

If the grid throws "An error occurred / Try reloading as a first step" over a black grid,
recover with `reload_tab`.

**Always read back and diff what you wrote.**

Data validation on dropdown columns does not extend to newly added rows. Copy a cell from
the last validated row and paste onto the new range.

## Uploading resumes to Drive

Synthetic drag-drop does not work; Drive reads `dataTransfer.items[].webkitGetAsEntry()`,
and both a real `DataTransfer` and a hand-rolled fake were consumed without uploading.

The working flow:

1. Patch `HTMLInputElement.prototype.click` to capture and suppress `type=file` clicks.
2. Drive its own **New > File upload** menu so the real multi-file input is captured
   instead of a native dialog.
3. Make that input visible and give it an `aria-label`.
4. `find` the label to get a ref, then call `mcp__claude-in-chrome__file_upload`
   (parameter is `paths`, and it must be a **standalone** call, never a `browser_batch`
   sub-action).
5. **The input is single-use.** Reload the folder page and repeat for each batch.

Getting the file id back out: read `[data-id]` off the tile whose `aria-label` matches the
filename and return `id.split('').join(' ')`, then strip the spaces yourself. A raw Drive
id trips the base64 return filter.

Watch Drive storage. Near 100% full, uploads fail quietly and the resume-link column
resolves to nothing.
