p.249 

목록 화면에서 게시글의 정보와 함께 댓글의 수를 같이 가져오기 위해서는 단순히 하나의 엔티티 타입을 이용할 수 없습니다. 이에 대한 해결책 중에서 가장 많이 쓰이는 방식은 JPQL의 조인을 이용해서 처리하는 
방식입니다. 

left(outer) join
조인을 할 떄 'INNER JOIN' 혹은 'JOIN' 과 같이 일반적인 조인을 이용할 수도 있고 'LEFT OUTER JOIN' 혹은 'LEFT JOIN' 을 이용할 수 있습니다.

### 엔티티 클래스 내부에 연관관계가 있는 경우 
Board 엔티티 클래스 내부에는 Member 엔티티 클래스를 변수로 선언하고, 연관관계를 맺고 있습니다. 이러한 경우에는 Board의 writer변수를 이용해서 조인을 처리합니다.
getGuestbookWithWriter()는 Guestbook(책에서는 Board) 을 사용하고 있지만 Member를 같이 조회해야 하는 상황입니다. 
Guestbook 클래스에는 Member와의 연관관계를 맺고 있으므로 g.writer와 같은 형태를 사용합니다. 이처럼 **내부에 있는 엔티티를 이용할 때는 
'LEFT JOIN' 뒤에 'ON'을 이용하는 부분이 없습니다**

### 연관관계가 없는 엔티티 조인 처리에는 on

p.251

Board와 Member사이에는 내부적으로 참조를 통해서 연관관계가 있지만, Board와 Reply는 좀 상황이 다릅니다. Reply쪽이 @ManyToOne으로 참조하고 있으나 Board입장에서는 Reply객체들을 
참조하고 있지 않기 때문에 문제가 됩니다. 
이런 경우에는 직접 조인에 필요한 조건은 'on'을 이용해서 작성해주어야 합니다. 
'특정 게시물과 해당 게시물에 속한 댓글들을 조회' 해야 하는 상황을 생각하면 board 와 reply 테이블을 조인해서 쿼리를 작성하게 됩니다. 
예를 들어 현재 데이터베이스 내에 100번 게시물은 두개의 댓글을 가지고 있는 상황입니다. 이를 순수한 SQL로 처리한다면 다음과 같이 작성할 수 있습니다. 
```
select
    board.bno, board.title, board.writer_email,
    rno, text
from board left outer join reply
on reply.board_bno = board.bno
where board.bno = 100;
```
이를 JPQL로 작성하면
```
@Query("SELECT g, r From Guestbook g LEFT JOIN Reply r ON r.guestbook = g WHERE g.gno = :gno")
    List<Object[]> getGuestbookWithReply(@Param("gno") Long gno);
```
