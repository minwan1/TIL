## 데이터베이스
관계형 데이터베이스의 특징을 한문장으로 요약하자면, 정규화 이론을 활용하여 데이터이상 현상을 제거하고, 데이터 중복을 최소화하여 데이터를 관리하는것이다.

### 내부조인(INNER JOIN)
내부 조인은 가장 일반적인 조인 형태로 많이 사용되는 기법이다. 내부조인은 둘이상의 테이블에 존재하는 공통 컬럼의 값이 같은것을 결과로 추출한다. 내부 조인에는 동등조인,자연조인,교차 조인으로 구분한다.

#### -동등 조인(EQUI JOIN)
조인의 가장 일반적인 활용 형식이다. 이름에서도 알수 있듯이 둘이상의 테이블에 존재하는 공통 컬럼의 속성을 비교하며, 부등호 조인은 동등 조인에 포함하지않는다.

```sql
select * from emp INNER JOIN dept ON emp.deptno = dept.deptno
```
위와같이 호출하게되면 emp와 dept테이블에 deptno 컬럼에 value가 같으면 두개의 테이블의 조인되어진 모든 컬럼들을 불러오게된다.
#### -자연조인(NATURAL JOIN)
자연조인은 동등조인과 거의 유사하다. 단 조인 대상 테이블의 모든 컬럼을 비교하여 같은 컬럼명을 가진 컬럼으로 조인을 수행한다. 둘의 차이점은 위에 동등조인같은경우에는 deptno 가 두번 추출된다. 하지만  밑에와 같이 sql을 실행하게되면 deptno컬럼이 한개만 추출된다.
```sql
select * from emp NATURAL JOIN dept
```
### 외부조인(OUTER JOIN)
외부조인은 조인 대상 테이블에서 특정 테이블의 데이터가 모두 필요한 상황이 있다. 이러한 요구 사항이 발생했을 때, 외부조인을 활용하여 효과적으로 결과 집합을 생성할 수 있다. 외부조인은 3가지 조인이 있다. 왼쪽외부조인,

#### left join
```sql
select
  emp.id, emp.first_name || emp.last_name as "NAME", dep.department_name
FROM
  dep LEFT OUTER JOIN emp
ON
  emp.id = dep.id
```

여기에서 LEFT OUTER JOIN이걸빼게되면 이너 조인과 같은 결과를 볼 수 있다.하지만 여기에서 의미하는 LEFT조인은 왼쪽 테이블에 정보를 더보여주라는것을 의미한다. 아래는 결과물이다.
![](http://i.imgur.com/8kubv8F.png)
이러한 조회를 통해 회사에 불필요한 부서를 알아 낼 수 도 있는지 조회해볼 수 있다. 만약 내부조인 같은경우에는 해당 조인 조건에 부합되는 결과만 나오기 때문에 저런 결과를 알 수 없다. 이러한 특별한 의도로 정보를 보고 싶을때 사용되는것이 외부 조인이다.


![](http://i.imgur.com/I5LlUNN.png)


참고
* [ngelmaum notes](http://blog.ngelmaum.org/entry/lab-note-sql-join-method)
