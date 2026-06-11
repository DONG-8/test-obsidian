
# Dataview - 리스트로 보기
```dataview
list
from "Atlas/Books"
sort file.name asc
```

---

# Dataview - 테이블로 보기
Books 폴더에 있는 모든 노트를 Table UI로 보여주기
```dataview
Table
	author as Author,
	("![|50](" + cover_url + ")") as Cover,
	dateformat(start_read_date, "yy.MM.dd") as Start,
	dateformat(finish_read_date, "yy.MM.dd") as End 
From
	"Atlas/Books"
Sort
	Start DESC
```

---

# Dataview - 테이블로 보기 (완독 필터)
```dataview
Table
	author as Author,
	("![|50](" + cover_url + ")") as Cover,
	dateformat(start_read_date, "yy.MM.dd") as Start,
	dateformat(finish_read_date, "yy.MM.dd") as End 
From
	"Atlas/Books"
Where
	status = "완독"
Sort
	Start DESC
```

---

# Dataview JS - 연결된 노트 자동 MOC 생성
```dataviewjs
// 현재 파일의 이름을 가져옵니다 (확장자 제외)
const currentFileName = dv.current().file.name.replace(/\.md$/, '');

// 'up' 속성에 현재 파일 이름이 포함된 페이지를 필터링하는 함수
function checkUpProperty(up, targetName) {
  if (!up) return false;
  
  // up이 배열인 경우
  if (Array.isArray(up)) {
    return up.some(item => checkUpProperty(item, targetName));
  }
  
  // up이 Obsidian 내부 링크 객체인 경우
  if (typeof up === 'object' && up.path) {
    return up.path === targetName || up.path.endsWith(`${targetName}.md`);
  }
  
  return false;
}

// 지정된 경로의 모든 페이지에서 'up' 속성에 현재 파일 이름을 포함하는 페이지를 필터링합니다
const pages = dv.pages()
  .where(p => checkUpProperty(p.up, currentFileName));

// 테이블 출력
dv.table(["File", "UP", "Folder"],
  pages
    .sort(p => p.file.name, 'asc')
    .map(p => [
      p.file.link, 
      Array.isArray(p.up) ? p.up.join(", ") : "",
      p.file.folder.split('/').pop()
    ])
);
```


---

# Dataview - 6F Home
```dataview
TABLE WITHOUT ID
    regexreplace(file.path, "/[^/]+$", "") as folder,
    file.link as name,
    up
FROM
    "Efforts" AND -"Efforts/Archive"
SORT
	regexreplace(file.path, "/[^/]+$", "") ASC
```

---

# Dataview - 6F 컬렉션

## Efforts
```dataviewjs
// 현재 파일의 이름을 가져옵니다 (확장자 제외)
const currentFileName = dv.current().file.name.replace(/\.md$/, '');

// 'up' 속성에 현재 파일 이름이 포함된 페이지를 필터링하는 함수
function checkUpProperty(up, targetName) {
  if (!up) return false;

  // up이 배열인 경우
  if (Array.isArray(up)) {
    return up.some(item => checkUpProperty(item, targetName));
  }

  // up이 Obsidian 내부 링크 객체인 경우
  if (typeof up === 'object' && up.path) {
    return up.path === targetName || up.path.endsWith(`${targetName}.md`);
  }

  return false;
}

// 폴더에 있는 페이지만 필터링
const pages = dv.pages('"Efforts"')
  .where(p => 
    p.file.path.startsWith("Efforts/On") ||
    p.file.path.startsWith("Efforts/Ongoing") ||
    p.file.path.startsWith("Efforts/Simmering") ||
    p.file.path.startsWith("Efforts/Sleeping")
  )
  .where(p => checkUpProperty(p.up, currentFileName));

// 테이블 출력
dv.table(["Status", "File", "UP"],
  pages.map(p => {
    const folderName = p.file.path.split('/').slice(-2, -1)[0];

    return [
      folderName,
      p.file.link,
      Array.isArray(p.up) ? p.up.join(", ") : ""
    ];
  })
);
```

## LOG
```dataviewjs
// 현재 파일의 이름을 가져옵니다 (확장자 제외)
const currentFileName = dv.current().file.name.replace(/\.md$/, '');

// 'up' 속성에 현재 파일 이름이 포함된 페이지를 필터링하는 함수
function checkUpProperty(up, targetName) {
  if (!up) return false;
  
  // up이 배열인 경우
  if (Array.isArray(up)) {
    return up.some(item => checkUpProperty(item, targetName));
  }
  
  // up이 Obsidian 내부 링크 객체인 경우
  if (typeof up === 'object' && up.path) {
    return up.path === targetName || up.path.endsWith(`${targetName}.md`);
  }
  
  return false;
}

// 지정된 경로의 모든 페이지에서 'up' 속성에 현재 파일 이름을 포함하는 페이지를 필터링합니다
const pages = dv.pages('"Calendar/Logs"')
  .where(p => checkUpProperty(p.up, currentFileName));

// 테이블 출력
dv.table(["File", "UP"],
  pages.map(p => [
      p.file.link, 
      Array.isArray(p.up) ? p.up.join(", ") : ""
    ])
);
```

## MOC
```dataviewjs
// 현재 파일의 이름을 가져옵니다 (확장자 제외)
const currentFileName = dv.current().file.name.replace(/\.md$/, '');

// 'up' 속성에 현재 파일 이름을 포함된 페이지를 필터링하는 함수
function checkUpProperty(up, targetName) {
  if (!up) return false;
  
  // up이 배열인 경우
  if (Array.isArray(up)) {
    return up.some(item => checkUpProperty(item, targetName));
  }
  
  // up이 Obsidian 내부 링크 객체인 경우
  if (typeof up === 'object' && up.path) {
    return up.path === targetName || up.path.endsWith(`${targetName}.md`);
  }
  
  return false;
}

// 지정된 경로의 모든 페이지에서 'up' 속성에 현재 파일 이름을 포함하는 페이지를 필터링하고 파일명 기준으로 정렬
const sortedPages = dv.pages('"Atlas/Maps"')
  .where(p => checkUpProperty(p.up, currentFileName))
  .sort(p => p.file.name);

// 테이블 출력
dv.table(["File", "UP"],
  sortedPages.map(p => [
    p.file.link,
    Array.isArray(p.up) ? p.up.join(", ") : ""
  ])
);
```

## Archive
```dataviewjs
// 현재 파일의 이름을 가져옵니다 (확장자 제외)
const currentFileName = dv.current().file.name.replace(/\.md$/, '');

// 'up' 속성에 현재 파일 이름이 포함된 페이지를 필터링하는 함수
function checkUpProperty(up, targetName) {
  if (!up) return false;

  // up이 배열인 경우
  if (Array.isArray(up)) {
    return up.some(item => checkUpProperty(item, targetName));
  }

  // up이 Obsidian 내부 링크 객체인 경우
  if (typeof up === 'object' && up.path) {
    return up.path === targetName || up.path.endsWith(`${targetName}.md`);
  }

  return false;
}

// 지정된 경로의 모든 페이지에서 'up' 속성에 현재 파일 이름을 포함하는 페이지를 필터링합니다
const pages = dv.pages('"Efforts/Archive"')
  .where(p => checkUpProperty(p.up, currentFileName));

// 테이블 출력
dv.table(["Status", "File", "UP"],
  pages.map(p => {
    const folderName = p.file.path.split('/').slice(-2, -1)[0];

    return [
      folderName,               // Status
      p.file.link,              // File
      Array.isArray(p.up) ? p.up.join(", ") : ""  // UP
    ];
  })
);
```