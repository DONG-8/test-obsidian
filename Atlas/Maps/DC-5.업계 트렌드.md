---
up:
  - "[[♳ DamonCompany]]"
---

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
