---
parent:
  - "[[Areas]]"
---

## Children

```dataviewjs
function findChildren(pageFile, maxDepth = 99, depth = 0, parentChildren = null) {
    let allChildren = [];
    if (depth >= maxDepth){
	    return allChildren;
    }
    let pageName = pageFile.file.name;
    let children = dv.pages()
        .where(p => p.file.frontmatter.parent && p.file.frontmatter.parent.includes("[[" + pageName + "]]"))
        .map(p => ({ page: p, depth: depth }));

    if (parentChildren != null) {
        parentChildren[parentChildren.length - 1].isLastChild = children.length === 0;
    }

    children.forEach((child, index) => {
        child.isLastChild = index === children.length - 1; // Mark if it's the last child
        allChildren.push(child);
        allChildren = allChildren.concat(findChildren(child.page, maxDepth, depth + 1, children));
    });

    return allChildren;
}

function displayHierarchy(children, showDepth = 99) {
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
        if (child.depth <= showDepth){
	        rows.push([prefix + child.page.file.name, fileLink]);
        }
    });
    return rows;
}

let currentPageName = dv.current();
let childPages = findChildren(currentPageName);
dv.table(["Child Pages", "Link"], displayHierarchy(childPages));

```

## Body
