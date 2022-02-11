p.241

두 개 이상의 엔티티 간의 연관관계를 맺고 나면 쿼리를 실행하는 데이터베이스 입장에서는 한가지 고민이 생깁니다. 
그것은 엔티티 클래스들이 실제 데이터베이스상에서는 두개 혹은 두개 이상의 테이블로 생성되기 때문에 연관관계를 맺고 있다는 것은 데이터베이스의 입장으로 보면 조인이 필요하다는 것입니다.
실제로 **@ManyToOne의 경우에는 FK 쪽의 엔티티를 가져올 때 PK 쪽의 엔티티도 가져옵니다.**

실행된 SQL 을 보면 reply, Board, member 테이블까지 모두 조인으로 처리되는 것을 볼 수 있습니다. Reply를 가져올때 매번 Board와 Member까지 조인해서 가져올 필요는 많지 않으므로 위와 같이 여러 
테이블이 조인으로 처리되는 상황은 그다지 효율적이지 않습니다. 

'즉시로딩'은 한번에 연관관계가 있는 모든 엔티티를 가져온다는 장점이 있지만, 여러 연관관계를 맺고 있거나 연관관계가 복잡할수록 조인으로 인한 성능저하를 피할 수 없습니다. 
**JPA에서 연관관계의 데이터를 어떻게 가져올 것인가를 fetch(패치)** 라고 하는데 연관관계의 어노테이션 속성으로 'fetch'모드를 지정합니다. 

'즉시로딩'은 불필요한 조인까지 처리해야하는 경우가 많기 때문에 가능하면 사용하지 않고 그와 반대되는 개념으로 'Lazy loading'으로 처리합니다.

Guestbook (책에서는 Board)클래스에서ㅏ fetch속성을 지연로딩으로 적용하고 testRead1()을 실행하면 이전 실행 결과와 달리 예외가 발생합니다. 
예외 메시지는  could not initialize proxy [com.example.guestbook.entity.Member#user100aaa.com] - no Session
데이터베이스와 추가적인 연결이 필요하다는 것.
지연로딩 방식으로 로딩하기 때문에 Guestbook테이블만을 가져와서 프린트 하는것은 문제 없지만 guestbook.getWriter()에서 문제 발생 
guestbook.getWriter()는 member 테이블을 로딩해야하는데 이미 데이터베이스와의 연결은 끝난 상태이기 때문이다.
이때 필요한것이 **@Transactional **
다시 한번 데이터베이스와의 연결을 해줌 
```java
 @ManyToOne(fetch = FetchType.LAZY)
    private Member writer;

```


```java
    @Transactional
    @Test
    public void testRead1(){
        Optional<Guestbook> result = guestbookRepository.findById(100L);
        Guestbook guestbook  = result.get();
        System.out.println(guestbook);
        System.out.println(guestbook.getWriter());
    }
```

p.248
엔티티간의 연관관계를 지정하는 경우에는 항상 @ToString()을 주의해야 합니다.
@ToString()은 해당클래스의 모든 멤버 변수를 출력하게 됩니다. 
이때 데이터베이스와의 연결이 필요하게 된다. 
이런 문제로 인해 연관관계가 있는 엔티티 클래스의 경우 @ToString() 을 할 때는 습관적으로 exclude 속성을 사용하는것이 좋다 
exclude는 해당 속성값으로 지정된 변수 는 toString()에서 제외하기 때문에 지연 로딩을 할 경우에는 반드시 지정해주는 것이 좋다 

## 지연로딩의 장단점 
지연로딩은 조인을 하지 않기 때문에 단순하게 하나의 테이블을 이용하는 경우에는 빠른 속도의 처리가 가능하다는 장점이 있습니다. 반면에 위와 같이 필요한 순간에 쿼리를 실행해야 하기 때문에 연관관계가 복잡경우에는
여러번의 쿼리가 실행된다는 단점이 있습니다. 

따라서 보편적인 코딩 가이드는 '지연로딩을 기본으로 사용하고 , 상황에 맞게 필요한 방법을 찾는다' 입니다. 
