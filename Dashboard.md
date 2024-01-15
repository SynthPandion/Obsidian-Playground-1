### Current Time `=date(now)`

笔记总数: `$=dv.pages().length` 条

```dataviewjs
async function calculateTotalWordsAndCharacters() {
    let totalCount = 0;
    for (const page of dv.pages()) {
        const content = await dv.io.load(page.file.path);
        const words = content.match(/\b[\w']+\b/g) || [];
        const chineseCharacters = content.match(/[\u3400-\u9FBF]/g) || [];
        totalCount += words.length + chineseCharacters.length;
    }
    dv.paragraph("笔记总字数: " + totalCount);
}

calculateTotalWordsAndCharacters();
```

### Created Today

```dataview
LIST
WHERE
	file.cday = date(today)
```

### Updated Today

```dataview
LIST
WHERE
	file.mday = date(today)
```

### Updated Recent

```dataview
TABLE WITHOUT ID
	file.link AS "File",
	(date(today) - date(file.mday)).days AS "Days Since Last Modification"
FROM ""
SORT file.mtime DESC
LIMIT 30
```
