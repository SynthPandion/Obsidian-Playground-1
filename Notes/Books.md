---
parent:
  - "[[Database]]"
---

## Children

```dataview
LIST
WHERE
	contains(file.frontmatter.parent, "[[" + this.file.name + "]]")
```

## Body
