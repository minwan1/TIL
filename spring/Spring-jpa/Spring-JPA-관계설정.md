
# JPA 관계 설정
<!-- TOC -->

- [JPA 관계 설정](#jpa-관계-설정)
    - [JPA 관계 설정이란](#jpa-관계-설정이란)
    - [단방향 연관관계 매핑](#단방향-연관관계-매핑)
        - [일대다 단방향[1:N]](#일대다-단방향1n)
        - [일대다 단방향[1:N] 예제](#일대다-단방향1n-예제)
        - [다대일 단방향[N:1]](#다대일-단방향n1)
        - [다대일 단방향[N:1] 예제](#다대일-단방향n1-예제)
    - [다대일 양방향[N:1]](#다대일-양방향n1)
        - [다대일 양방향[N:1] 예제](#다대일-양방향n1-예제)
        - [편의 메소드](#편의-메소드)
    - [다대다(N:N) 양방향](#다대다nn-양방향)
        - [다대다(N:N) 양방향 예제](#다대다nn-양방향-예제)
    - [다대다 연관관계 정리](#다대다-연관관계-정리)
    - [마치며](#마치며)

<!-- /TOC -->
## JPA 관계 설정이란
엔터티 또는 도메인들은 각각 관계를 맺고 있습니다. 예를 들어 유저는 여러 개의 제품을 주문할 수 있다고 하면 유저 엔터티는 주문 엔터티와 관계를 갖습니다. 또 주문 엔터티는 어떠한 제품이 주문됐는지 알아야 하기 때문에 제품엔터티와 관계를 맺습니다.

JPA에서 관계 설정을 하게 되면 알아서 객체에서는 참조를 사용해서 관계를 맺고 테이블은 외래 키를 통해 관계를 맺은 후 서로 매핑을 해놓습니다. 이제 이렇게 되면 개발자는 객체 간의 관계 또 그사이에서 생성되어야만 하는 SQL 문들을 신경 쓸 필요 없이 객체 간의 관계만 신경 쓰면 되기 때문에 비즈니스 로직에 집중할 수 있습니다.

**방향**
관계 설정은 단방향, 양방향으로 관계를 맺을 수 있습니다. 예를 들어 회원 -> 주문, 주문 -> 회원 한쪽만 참고가 필요하다면 단순 당 방향으로 설정할 수 있고, 반대로 양쪽 다 회원 -> 주문, 주문 -> 회원을 둘 다 참고해야 한다면 양방향으로 설정할 수 있습니다. **방향은 객체 관계에만 존재하고 테이블 관계는 항상 양방향입니다.**


**연관관계의 주인**
객체를 양방향 연관 관계로 만들면 연관관계의 주인을 정해야 합니다. 이러한 주인은 보통 외래 키를 관리하는 엔터티가 주인이 됩니다. 연관 관계의 주인을 정한다는 것은 사실 외래 키 관리자를 선택하는 것입니다.

**관계 종류**
관계 설정은 일대일, 일대다, 다대일, 다대다 관계가 있을 수 있습니다.

**@JoinColumn**

이 어노테이션은 객체 간에 관계를 설정한 후 외래 키를 매핑할 때 사용합니다. 그냥 객체 간에 관계 설정을 해주면 필드 + _ + 참조하는 테이블의 기본 키 칼럼명으로 가져가게 됩니다.

|속성|기능|기본값|
|-|-|-|
|name |매핑할 외래 키 이름 | 필드 + _ + 참조하는 테이블의 기본키 컬럼명 |
| referencedColumnName | 외래키가 참조하는 대상 테이블의 컬럼명 |참조하는 테이블의 기본키 컬럼명 |
| foreignKey(DDL) | 외래 키 제약 조건을 직접 지정할 수 있다.<br> 이 속성은 테이블을 생성할 때만 사용한다. |

아래에서는 1:N, N:1, N:N에 관계 설정을 하겠습니다.

## 단방향 연관관계 매핑

### 일대다 단방향[1:N]
일대다 단방향 관계 설정을 해보겠습니다. 예제는 아래의 테이블을 기반으로 설명하겠습니다. 아래는 회원과 주문테이블이 존재합니다. 회원은 자기가 주문한 정보를 참조할 필요가 있습니다. 그런데 주문에서는 회원정보를 참조할 필요가 없을 경우 일대다 단방향을 구성할 수 있습니다.

![](https://i.imgur.com/aQdhBN6.png)

### 일대다 단방향[1:N] 예제
그렇게 되면 다음과 같이 1:N 단방향 관계 설정을 할 수 있습니다.
```java
@Entity
@Getter
@Table(name = "member")
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Member {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id")
    private long id;

    @OneToMany
    @JoinColumn(name = "member_id")
    private List<Order> orders= new ArrayList<>();

}
```

```java
@Getter
@Entity
@Table(name = "purchase_order")
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Order {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id")
    private long id;

}
```

**일대다 단방향 관계 주의할점**
일대다 단방향 관계를 매핑할 때는 @JoinColumn을 명시해야 합니다. 그렇지 않으면 JPA는 연결 테이블을 중간에 두고 연관관계를 관리하는 조인 테이블 전략을 기본으로 사용해서 매핑하게 됩니다.

**일대다 단방향 매핑의 단점**
일대다 단방향 단점은 매핑 한 객체가 관리하는 외래 키가 다른 테이블에 있다는 점입니다. 본인 테이블에 외래 키가 있으면 엔터티의 저장과 연관 관계 처리를 INSERT SQL 한 번으로 끝낼 수 있지만, 다른 테이블에 외래 키가 있으면 연관관계 처리를 위한 UPDATE SQL을 추가로 실행해야 합니다.

```sql
Hibernate: insert into purchase_order values ( )
Hibernate: insert into purchase_order values ( )
Hibernate: insert into member values ( )
Hibernate: update purchase_order set member_id=? where id=?
Hibernate: update purchase_order set member_id=? where id=?

```

다대일 단방향 또는 다대일 양방향은 아래와 같이 insert문 만으로 데이터가 테이블에 넣어집니다.

```sql
Hibernate: insert into member values ( )
Hibernate: insert into purchase_order (member_id) values (?)
Hibernate: insert into purchase_order (member_id) values (?)
```

### 다대일 단방향[N:1]
이번에는 반대로 회원 객체로는 주문한 정보를 참조할 일이 없고, 주문 객체가 회원정보를 참조해야 한다면 아까와 반대로 N:1 단방향의 관계 설정을 할 수 있을 것입니다. 다음과 같이 설정할 수 있습니다.

### 다대일 단방향[N:1] 예제

```java
@Entity
@Getter
@Table(name = "member")
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Member {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id")
    private long id;

}

```


```java


@Getter
@Entity
@Table(name = "purchase_order")
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Order {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id")
    private long id;

    @ManyToOne
    @JoinColumn(name = "member_id", nullable = false, updatable = false)
    private Member member;

}

```




## 다대일 양방향[N:1]
양방향 연관관계 설정은 주로 member에서도 order 객체를 참조할 일이 있고, order 객체에서도 member 객체를 참조해야 할 일이 있을 때 사용할 수 있습니다.

### 다대일 양방향[N:1] 예제
다음은 회원과 주문은 일대다 관계 설정을 양방향 관계로 한 예제입니다. 일대다 관계를 매핑하기 위해 @OneToMany 매핑 정보를 사용했습니다. mappedBy 속성은 양방향 매핑일 때 사용하는데 반대쪽 매핑의 필드 이름을 값으로 주면 됩니다. 이렇게되면 서로 참조할 수 있는 구조를 가지게됩니다.

```java
@Entity
@Getter
@Table(name = "member")
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Member {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id")
    private long id;

    @OneToMany(mappedBy = "member")
    private List<Order> orders= new ArrayList<>();

}
```


```java
@Getter
@Entity
@Table(name = "purchase_order")
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Order {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id")
    private long id;

    @ManyToOne
    @JoinColumn(name = "member_id", nullable = false, updatable = false)
    private Member member;

}

```


### 편의 메소드
양방향 연관관계는 항상 서로 참조해야 합니다. 어느 한쪽만 참조하면 양방향 연관관계가 성립하지 않습니다. 물론 양방향 연관 관계의 주인만 변경해줘도 데이터베이스 외래 키 변경에 문제를 가지지는 않습니다. 

하지만 영속성 컨텍스트 내에서 연관관계 주인만 변경하고 참조되는 쪽에서 조회를 하면 변경되기 전 내용이 조회되기 때문에 문제를 가질 수 있습니다. 

먄약 주문내역에 회원이 변경된다면 다음과 같이 객체에 관계도 정리를 해줘야 합니다. 그렇기 때문에 주인이 변경될 때같이 참조되어 지는 쪽도 변경되어야 합니다. 위의 예제인 회원과 주문을 기반으로 편의 메서드를 만들어보겠습니다.

* 회원을 변경하려는 주문 내역에 회원에서 해당 주문 내역을 제거한다.
* 회원을 변경하려는 주문 내역에 새로운 회원을 설정한다.
* 변경된 주문 내역 회원에도 해당 변경된 주문 내역을 설정해준다.

위의 설명을 소스로 표현하면 다음과 같은 편의 메소드를 제공할 수 있습니다.

```java
public void setMember(Member member) {
    if(this.member !=null){
      this.member.getOrders().remove(this);
    }
    this.member = member;
    member.getOrders().add(this);
}
```


## 다대다(N:N) 양방향

관계형 데잍터베이스는 정규화된 테이블 2개로 다대다 관계를 표현할 수 없습니다. 그래서 보통 다대다 관계를 일대다, 다대일 관계로 풀어내는 연결 테이블을 사용합니다. 

그래서 다음과 같이 주문을 할 때 여러 개의 제품을 주문해야 한다면 중간에 order_line이라는 연결 테이블을 두어 표현할 수 있습니다. 이것을 표현하는 방법은 @OneToMany @ManyToOne, 를 이용하는 방법이나 @ManyToMany 또는 @CollectionTable 등을 이용하는 방법이 있습니다.

사실 @ManyToMany는 많이 사용되지 않는 어노테이션입니다. 보통 아래와 같이 테이블 구조를 가지게 되면 order_line에는 각각 제품에 주문량 주문금액 등이 표시되어야 하는데 @ManyToMany를 사용하면 order_line 테이블을 확장해나갈 수 없기 때문에 보통 @OneToMany @ManyToOne,@CollectionTable을 이용해 다대다를 풀어냅니다. 보통 @ManyToMany는 해당 매핑 테이블이 확장해나갈 일이 없는 경우에 사용할 수 있습니다.

![](https://i.imgur.com/Zb1iB6I.png)

여기에서는 ManyToMany 어노테이션을 이용해 테이블과 객체를 매핑할 것입니다.

@ManyToMany 속성

|속성|기능|
|-|-|
|name |매핑할 외래 키 이름 |
| joinColumns | 현재 테이블과 조인할 컬럼 명을 정한다. |
| inverseJoinColumns | 반대 방향인 상품과 매핑할 조인 컬럼 정보를 지정한다. |

### 다대다(N:N) 양방향 예제

```java
@Getter
@Entity
@Table(name = "purchase_order")
@NoArgsConstructor
public class Order {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id")
    private long id;

    @ManyToMany
    @JoinTable(name = "order_line",
            joinColumns = @JoinColumn(name = "order_id"),
            inverseJoinColumns = @JoinColumn(name = "product_id")
    )
    private List<Product> products = new ArrayList<>();

}

```



```java
@Entity
@Getter
@Table(name = "product")
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Product {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id")
    private long id;

    @ManyToMany(mappedBy = "products")
    private List<Order> orders = new ArrayList<>();
    
}

```



## 다대다 연관관계 정리
다대다 관계 설정이나 외래 키를 설정하다 보면 해당 외래 키를 사용하여 복합키를 사용할 건지 단순히 외래 키로 사용할지 선택해야 합니다. 아래는 이 외래 키를 가지고 어떻게 설정하느냐에 따라 식별 관계, 비실 별 관계가 되는지에 대한 설명입니다.

식별 관계 : 받아온 식별자를 기본 키 + 외래 키로 사용한다.
비식별 관계 : 받아온 식별자는 외래키로만 사용하고 새로운 식별자를 추가한다.

보통 JPA에서는 식별 관계를 사용하기 보다는 비식별관계로 사용합니다. 이유는 복합키를 위한 식별자 클래스를 만들지 않아도 되기 때문입니다.


[예제소스](https://github.com/minwan1/blog-example/tree/master/jpa-relation)

## 마치며
JPA에서 사용하는 관계 설정에 대해 짧게 알아봤습니다. 여기에서 중요한 것은 위에서 설명했던 예제 중에 상황에 맞게 잘 선택해서 관계 설정을 하면 된다는 것입니다. 굳이 사용하지 않는 참조를 사용해서 순환 참조를 만난다거나 의미 없는 도메인 관계를 만들 수 있기 때문입니다. 이렇게 되면 각각 도메인이 표현하고자 하는 범위를 넘어선 책임을 할 수 있기 때문에 좋지 않은 설계가 될 수 있습니다.


참고

* [자바 ORM 표준 JPA 프로그래밍](http://book.naver.com/bookdb/book_detail.nhn?bid=9252528)
