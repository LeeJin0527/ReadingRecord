p.145
본격적으로 Querydsl 의 실습은 다음과 같은 상황을 처리합니다 
- 제목/ 내용/ 작성자 와 같이 단 하나의 항목으로 검색하는 경우 
- '제목+내용' / '내용+ 작성자' / '제목+ 작성자'와 같이 2개의 항목으로 검색하는 경우 
- '제목+ 내용 + 작성자'와 같이 3개의 항목으로 검색하는 경우 
만일 Guestbook 엔티티 클래스에 많은 멤버 변수들이 선언 되어 있었다면 이러한 조합 수는 엄청 많아지게 됩니다. 이런 상황을 대비하여 상황에 맞게 쿼리를 처리할 수 있는 Qureydsl이 필요합니다.


Querydsl의 사용법은 다음과 같습니다. 
- BooleanBuilder를 생성합니다. 
- 조건에 맞는 구문은 Qureydsl에서 사용하는 Predicate 타입의 함수를 생성합니다. 
- BooleanBuilder에 작성된 Predicate를 추가하고 실행합니다. 

```java
//'title'로 검색
    @Test
    public void testQuery1(){
        Pageable pageable = PageRequest.of(0, 10, Sort.by("gno").descending());

        QGuestbook qGuestbook = QGuestbook.guestbook;
        String keyword = "1";
        BooleanBuilder builder = new BooleanBuilder();
        BooleanExpression expression = qGuestbook.title.contains(keyword);
        builder.and(expression);

        Page<Guestbook> result = guestbookRepository.findAll(builder, pageable);

        result.stream().forEach(guestbook -> {
            System.out.println(guestbook);
        });
    }
```
