## 페이징 처리
3장에서 말했듯이 수십 수백만개의 데이터를 메모리에 올리는것은 무리한 행동입니다. 이러한 대용량의 배치를 처리하기위해서 Srping batch에서는 여러가지 PagingItemReader를 제공합니다. 이것은 구현체는 크게 JdbcPagingItemReader, JpaPagingItemReader, HibernatePagingItemReader가 있습니다. 아래 예제에서는 JpaPagingItemReader를 사용하겠습니다. 

그런데 JpaPagingItemReader를 사용할때 주의해야할사항이 있습니다. 예를들어 아래와 같이 지금 유저 데이터로 31개의 유저 정보를 넣고있습니다.

```sql
INSERT INTO `member` (`id`, `created_at`, `name`, `process_status`, `updated_at`)
VALUES
	(1, '2018-05-18 15:23:25', 'test1', 'WAITING', '2018-05-18 15:23:25'),
	(2, '2018-05-18 15:23:25', 'test2', 'WAITING', '2018-05-18 15:23:25'),
	(3, '2018-05-18 15:23:25', 'test3', 'WAITING', '2018-05-18 15:23:25'),
	(4, '2018-05-18 15:23:25', 'test4', 'WAITING', '2018-05-18 15:23:25'),
	(5, '2018-05-18 15:23:25', 'test5', 'WAITING', '2018-05-18 15:23:25'),
	(6, '2018-05-18 15:23:25', 'test6', 'WAITING', '2018-05-18 15:23:25'),
	(7, '2018-05-18 15:23:25', 'test7', 'WAITING', '2018-05-18 15:23:25'),
	(8, '2018-05-18 15:23:25', 'test8', 'WAITING', '2018-05-18 15:23:25'),
	(9, '2018-05-18 15:23:25', 'test9', 'WAITING', '2018-05-18 15:23:25'),
	(10, '2018-05-18 15:23:25', 'test10', 'WAITING', '2018-05-18 15:23:25'),
	(11, '2018-05-18 15:23:25', 'test11', 'WAITING', '2018-05-18 15:23:25'),
	(12, '2018-05-18 15:23:25', 'test12', 'WAITING', '2018-05-18 15:23:25'),
	(13, '2018-05-18 15:23:25', 'test13', 'WAITING', '2018-05-18 15:23:25'),
	(14, '2018-05-18 15:23:25', 'test14', 'WAITING', '2018-05-18 15:23:25'),
	(15, '2018-05-18 15:23:25', 'test15', 'WAITING', '2018-05-18 15:23:25'),
	(16, '2018-05-18 15:23:25', 'test16', 'WAITING', '2018-05-18 15:23:25'),
	(17, '2018-05-18 15:23:25', 'test17', 'WAITING', '2018-05-18 15:23:25'),
	(18, '2018-05-18 15:23:25', 'test18', 'WAITING', '2018-05-18 15:23:25'),
	(19, '2018-05-18 15:23:25', 'test19', 'WAITING', '2018-05-18 15:23:25'),
	(20, '2018-05-18 15:23:25', 'test20', 'WAITING', '2018-05-18 15:23:25'),
	(21, '2018-05-18 15:23:25', 'test21', 'WAITING', '2018-05-18 15:23:25'),
	(22, '2018-05-18 15:23:25', 'test22', 'WAITING', '2018-05-18 15:23:25'),
	(23, '2018-05-18 15:23:25', 'test23', 'WAITING', '2018-05-18 15:23:25'),
	(24, '2018-05-18 15:23:25', 'test24', 'WAITING', '2018-05-18 15:23:25'),
	(25, '2018-05-18 15:23:25', 'test25', 'WAITING', '2018-05-18 15:23:25'),
	(26, '2018-05-18 15:23:25', 'test26', 'WAITING', '2018-05-18 15:23:25'),
	(27, '2018-05-18 15:23:25', 'test27', 'WAITING', '2018-05-18 15:23:25'),
	(28, '2018-05-18 15:23:25', 'test28', 'WAITING', '2018-05-18 15:23:25'),
	(29, '2018-05-18 15:23:25', 'test29', 'WAITING', '2018-05-18 15:23:25'),
	(30, '2018-05-18 15:23:25', 'test30', 'WAITING', '2018-05-18 15:23:25'),
	(31, '2018-05-18 15:23:25', 'test31', 'WAITING', '2018-05-18 15:23:25');

```

그럼 이것을 10개씩 페이징 처리를 하고 배치 처리를 한다고 가정해보겠습니다. 처음 10개의 유저정보가 WAITING 상태에서 COMPLETED로 변경될것입니다. 하지만 그 이후에 페이징 처리에서 offset 11이 될것입니다. 이렇게 되면 앞에 유저 10개를 제외하고 그다음 부터 페이징 처리를 하게됨으로써 제대로된 배치처리 프로그램을 만들 수 없습니다. 

그렇기 때문에 이러한 문제를 해결하려면 크게 2가지 방법이 있는데 하나는 JpaPagingReader offset 처리를 오버라리이딩 하거나 Cusor방법을 이용하는것입니다. 일반적으로 JpaPagingReader offset을 오버라이딩 하는 방식을 많이 사용합니다. 아래는 JpaPagingReader 설정입니다.

```java
 @Bean(destroyMethod = "")
    @StepScope
    public JpaPagingItemReader<Member> completeMemberReader(){
        JpaPagingItemReader<Member> jpaPagingItemReader =
                new JpaPagingItemReader(){
                    @Override
                    public int getPage() { //offset overriding
                        return 0;
                    }
                };

        jpaPagingItemReader.setQueryString("select m from Member as m where m" +
                ".createdAt < :createdAt and m.processStatus = :processStatus");

        Map<String, Object> map = new HashMap<>();
        LocalDateTime now = LocalDateTime.now();
        map.put("createdAt", now.minusYears(1));
        map.put("processStatus", ProcessStatus.WAITING);
        jpaPagingItemReader.setParameterValues(map);
        jpaPagingItemReader.setEntityManagerFactory(entityManagerFactory);
        jpaPagingItemReader.setPageSize(CHUNK_SIZE);
        return jpaPagingItemReader;
    }
```


그리고 스프링 배치시 주의사항으로 항상 ChunkSize와 PageSize는 같은 Size를 유지하는것이 좋습니다. 

```java
JpaPagingItemReader.class

	@Override
	@SuppressWarnings("unchecked")
	protected void doReadPage() {

		EntityTransaction tx = null;
		
		if (transacted) { //transaction이 존재한다면 transaction을 clear해줌.
			tx = entityManager.getTransaction();
			tx.begin();
			
			entityManager.flush();
			entityManager.clear();
		}//end if

		Query query = createQuery().setFirstResult(getPage() * getPageSize()).setMaxResults(getPageSize());

		if (parameterValues != null) {
			for (Map.Entry<String, Object> me : parameterValues.entrySet()) {
				query.setParameter(me.getKey(), me.getValue());
			}
		}

		if (results == null) {
			results = new CopyOnWriteArrayList<>();
		}
		else {
			results.clear();
		}
		
		if (!transacted) {
			List<T> queryResult = query.getResultList();
			for (T entity : queryResult) {
				entityManager.detach(entity);
				results.add(entity);
			}//end if
		} else {
			results.addAll(query.getResultList());
			tx.commit();
		}//end if
	}
```
그이유는 위에 소스를 보면 페이지를 읽어올때마다 기존에 Transaction이 있다면 clear해주기 때문입니다. 이렇게되면 Processor를처리하다가 LazyInitializationException을 만날 수 있씁니다. 

예를들어 커밋 단위는 10개이고 페이징처리는 5로 되어있다고 해보겠습니다. 그렇게되면 chunk size에 맞쳐서 doReadPage를 두번호출해야합니다. 이때 만약 Processor쪽에서 Lazy된 객체를 읽어온다면 LazyInitializationException 을 만나게 됩니다.


## Spring paging reader Update 2번이슈

```java
		if (transacted) { //transaction이 존재한다면 transaction을 clear해줌.
			tx = entityManager.getTransaction();
			tx.begin();
			
			entityManager.flush();
			entityManager.clear();
		}//end if
```