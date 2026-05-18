## Drafts Needing Review
```dataview
TABLE artifact_type, domain, last_updated, file.folder
FROM ""
WHERE status = "draft" OR status = "review"
SORT last_updated ASC
```
## Orphaned Notes
```dataview
TABLE file.folder, status, artifact_type, last_updated
FROM ""
WHERE length(file.inlinks) = 0 AND length(file.outlinks) = 0
AND !contains(file.path, "90-Local-Admin")
AND !contains(file.path, "99-Archived")
AND !contains(file.path, ".obsidian")
SORT file.folder ASC, file.name ASC
```
## Recently Updated
```dataview
TABLE artifact_type, status, file.folder
FROM ""
WHERE last_updated
SORT last_updated DESC
LIMIT 25
```
## Canonical Docs Needing Refresh
```dataview
TABLE artifact_type, domain, last_updated, file.folder
FROM ""
WHERE status = "canonical"
AND last_updated < date(today) - dur(90 days)
SORT last_updated ASC
```

