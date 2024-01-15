---
parent:
  - "[[Index]]"
---

## Children

```dataview
TABLE
	file.frontmatter.status as status,
	file.frontmatter.area as area
WHERE
	contains(file.frontmatter.parent, "[[" + this.file.name + "]]")
SORT
	file.frontmatter.status,
	file.frontmatter.area,
	file.name
```

```dataviewjs
const currentFileName = dv.current().file.name;
const files = dv.pages('')
    .where(p => p.file.frontmatter.parent?.includes("[[" + currentFileName + "]]"))
    .sort((a, b) => {
        const extractNumber = str => {
            if (typeof str === 'string') {
                const match = str.match(/\d+/);
                // Find the first sequence of digits
                return match ? parseInt(match[0], 10) : null;
            }
            return null;
        };

		const nameA = a.name;
		const nameB = b.name;

        const numA = extractNumber(nameA);
        const numB = extractNumber(nameB);

		if (numA !== null && numB !== null) {
            return numA - numB;
        } else if (nameA && nameB) {
            return nameA.localeCompare(nameB, undefined, {numeric: true});
        } else {
            return 0; // In case one of the names is not a valid string
        }
    });

dv.table(["File", "Status"], files.map(f => [f.file.link, f.file.frontmatter.status]));
```

```dataviewjs
function findChildren(pageFile, depth = 0, parentChildren = null) {
    let pageName = pageFile.file.name;
    let children = dv.pages()
        .where(p => p.file.frontmatter.parent && p.file.frontmatter.parent.includes("[[" + pageName + "]]"))
        .map(p => ({ page: p, depth: depth }));

    if (parentChildren != null) {
        parentChildren[parentChildren.length - 1].isLastChild = children.length === 0;
    }

    let allChildren = [];
    children.forEach((child, index) => {
        child.isLastChild = index === children.length - 1; // Mark if it's the last child
        allChildren.push(child);
        allChildren = allChildren.concat(findChildren(child.page, depth + 1, children));
    });

    return allChildren;
}

function displayHierarchy(children) {
    let rows = [];
    children.forEach(child => {
        let prefix = "";
        if (child.depth == 0){
            prefix = "### ";
        }
        else if (child.depth == 1){
	        if (child.isLastChild){
		        prefix = "└── ";
	        }
	        else{
		        prefix = "├── ";
	        }
        }
        else{
	        if (child.isLastChild){
		        prefix = "" + "│  ".repeat((child.depth - 1) * 1) + "└── ";
	        }
	        else{
		        prefix = "" + "│  ".repeat((child.depth - 1) * 1) + "├── ";
	        }
        }

        let linkPath = child.page.file.path;
        let fileLink = dv.fileLink(linkPath, false, "Link")
        let suffix = child.isLastChild ? " (Final Page)" : "";
	    rows.push([prefix + child.page.file.name, fileLink]);
    });
    return rows;
}

let currentPageName = dv.current();
let childPages = findChildren(currentPageName);
dv.table(["Child Pages", "Link"], displayHierarchy(childPages));

```

## Body
