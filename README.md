# FreespacePaths

JSON file maintained of common locations that can be cleared to free up disk space.
Consumed at runtime by [get-freespace](https://github.com/DailenG/get-freespace) via its
`-Source` parameter.

## Entry schema

```json
{
  "name": "Revit Journals",
  "path": "C:\\Users\\*\\AppData\\Local\\Autodesk\\Revit\\*Revit*202*\\Journals\\",
  "description": "Revit journal files for all users and 202x versions",
  "retain_days": 7,
  "scope": "file"
}
```

| Field | Required | Meaning |
| --- | --- | --- |
| `name` | yes | Display label shown in the selection UI. |
| `path` | yes | Target location. Wildcards allowed; expands to one target folder per matching user profile. |
| `description` | no | Free text. |
| `retain_days` | no | Keep anything whose activity is newer than this many days. `0` keeps nothing. Defaults to `0`. |
| `aged` | no | Deprecated alias for `retain_days`. Still honored so older clients keep working. Do not use in new entries. |
| `scope` | no | `file` (default) judges every file individually. `folder` judges each immediate child folder as a unit and keeps it whole if anything inside it is recent. |

Activity is the newest of `LastWriteTime`, `CreationTime` and, where NTFS still records
it, `LastAccessTime`. Components that are read constantly but rarely rewritten therefore
count as in use.

Pick `scope` by how the data is consumed:

- `file` for flat logs such as Revit journals, where each file stands alone.
- `folder` for caches that are only useful intact, such as `CollaborationCache`, where
  deleting part of a model's cache is worse than deleting all of it.

Consumers can raise every window at once with `-RetainDays N`, which acts as a floor over
whatever is published here. It never lowers a value set in this file.
