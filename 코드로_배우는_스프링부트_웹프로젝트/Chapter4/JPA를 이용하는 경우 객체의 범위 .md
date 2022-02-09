p.133

```java
@MappedSuperclass
@EntityListeners(value = {AuditingEntityListener.class})
@Getter
abstract class BaseEntity {
    @CreatedDate
    @Column(name = "regdate", updateable = false)
    private LocalDateTime regDate;

    @LastModifiedDate
    @Column(name = "moddate")
    private LocalDateTime modDate;
}
```
**@MappedSuperClass**: MappedSuperClass 어노테이션이 적용된 클래스는 테이블로 생성되지 않는다 . 실제로 테이블은 BaseEntity 클래스를 상속한 엔티티의 클래스로 데이터 베이스 테이블이 생성된다. 

p.134

![](https://images.velog.io/images/jinii/post/2a0b9881-82da-4949-903b-eafcd9d58185/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202022-02-09%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%205.46.18.png)

JPA는 JPA 만의 고유한 메모리 공간(이하 콘텍스트(context))을 이용해서 엔티티 객체들을 관리합니다. 기존의 MyBatis 기반의 프로그램과 달리 단계가 하나 더 있는 방식입니다.
MyBatis를 이용하는 경우에는 SQL을 위해서 전달되는 객체는 모두 SQL처리가 끝난 후에는 어떻게 되든 상관이 없는 객체들인 반면에 **JPA에서 사용하는 엔티티 객체들은 영속 콘텍스트라는 곳에서 관리되는 객체입니다. 
이 객체들이 변경되면 결과적으로 데이터베이스에 이를 반영하는 방식입니다.**
위의 그림에서 1, 2, 3의 경우 동일한 객체이건 전혀다른 객체이건 상관없지만 JPA방식에서는 해당 엔티티 객체는 유지되고 필요할 때 꺼내서 재사용하는 방식이 됩니다. 따라서 이러한 엔티티 객체에는
어떤 변화가 일어나는 것을 감지하는 **리스너**가 있습니다.

JPA내부에서 엔티티 객체가 생성/변경되는 것을 감지하는 역할은 AuditingEntityListener로 이루어집니다. 
이를 사용하기 위해서 메인 메소드에 @EnableJpaAuditing 추가


