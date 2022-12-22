---
cssclass: dashboard
---
# By Size
List all my files by size. I prioritize the smaller notes.
```dataview
TABLE
	description AS Description,
	file.size AS Size
FROM "" AND -"_templates" AND -"_resources"
WHERE file.name!="Home"
SORT file.size asc
```
# By Type
List all of my files by their type attribute.

## Tutorials
My how-to-style articles.
```dataview
LIST
WHERE type="tutorial"
```
## Writing
My long-form articles.
```dataview
LIST
WHERE type="writing"

```
