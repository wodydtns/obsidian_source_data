# 독서 목록

```dataview
table 저자 as "저자", 출판사 as "출판사", 날짜 as "추가 날짜"
from "Projects/독서리스트"
where 상태 = "읽고 싶은 책"
sort 날짜 desc


```



