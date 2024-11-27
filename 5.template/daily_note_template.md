---
title: <% tp.file.title %>
created: <% tp.date.now("YYYY-MM-DD @ HH:mm") %>
updated: <% tp.date.now("YYYY-MM-DD @ HH:mm") %>
tag: dailynote
---
<%*
const title = tp.file.title;
const yesterday = moment(title).subtract(1, 'days').format("YYYY-MM-DD");
const tomorrow = moment(title).add(1, 'days').format("YYYY-MM-DD");
%>
# <% title %>
<% tp.web.daily_quote() %>

↶ [[<% yesterday %>]] | [[<% tomorrow %>]] ↷

## 중요한 일정
- [ ] 일정 1
- [ ] 일정 2

## 할 일
- [ ] 할 일 1
- [ ] 할 일 2

## 노트
- 

## 감사 일기
-