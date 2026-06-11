---
up: "[[Part 2. 디자인 스타일 세팅]]"
---

```
/* H1~H6 행간 설정  */
.cm-header-1 {
    line-height: 2.0;
}
.cm-header-2 {
    line-height: 2.0;
}
.cm-header-3 {
    line-height: 2.0;
}
.cm-header-4 {
    line-height: 1.8;
}
.cm-header-5 {
    line-height: 1.8;
}
.cm-header-6 {
    line-height: 1.8;
} 

/* 텍스트 행간 설정 */
.cm-contentContainer { 
    line-height: 1.8;
} 

/* embed 영역 디자인 설정 */
.markdown-embed { 
    border: 1px solid #E7E5E4;
    border-radius: 5px;
    margin: 0;
    padding: 10px;
} 

/* 체크박스 스트라이크 제거 */
.markdown-source-view.mod-cm6 .HyperMD-task-line[data-task]:not([data-task=" "]) {
    text-decoration:none;
    color: var(--checklist-done-color); 
} 
```