---
cssclass: dashboard
---
# Home

## By Size
```dataview
TABLE
	description AS Description,
	file.size AS Size
FROM "" AND -"_templates" AND -"_resources"
WHERE file.name!="Home"
SORT file.size asc
```

## By Type
### Tutorials
```dataview
LIST
WHERE type="tutorial"
```
### Writing
```dataview
LIST
WHERE type="writing"
```
