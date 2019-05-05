


## 페이징 처리



```java
    @Bean(destroyMethod = "")
    @StepScope
    public JpaPagingItemReader<User> inactiveUserJpaReader(){
        JpaPagingItemReader<User> jpaPagingItemReader =
                new JpaPagingItemReader(){
                    @Override
                    public int getPage() {
                        return 0;
                    }
                };
        jpaPagingItemReader.setQueryString("select u from User as u where u" +
                ".updatedDate < :updatedDate and u.status = :status");


        Map<String, Object> map = new HashMap<>();
        LocalDateTime now = LocalDateTime.now();
        map.put("updatedDate", now.minusYears(1));
        map.put("status", UserStatus.ACTIVE);
        jpaPagingItemReader.setParameterValues(map);

        jpaPagingItemReader.setEntityManagerFactory(entityManagerFactory);
        jpaPagingItemReader.setPageSize(CHUNK_SIZE);
        return jpaPagingItemReader;
    }
```




```java
public class SimpleChunkProvider<I> implements ChunkProvider<I> {
    @Override
	public Chunk<I> provide(final StepContribution contribution) throws Exception {

		final Chunk<I> inputs = new Chunk<I>();
		repeatOperations.iterate(new RepeatCallback() {

			@Override
			public RepeatStatus doInIteration(final RepeatContext context) throws Exception {
				I item = null;
				try {
					item = read(contribution, inputs);
				}
				catch (SkipOverflowException e) {
					// read() tells us about an excess of skips by throwing an
					// exception
					return RepeatStatus.FINISHED;
				}
				if (item == null) {
					inputs.setEnd();
					return RepeatStatus.FINISHED;
				}
				inputs.add(item);
				contribution.incrementReadCount();
				return RepeatStatus.CONTINUABLE;
			}

		});
		return inputs;
	}

    protected I read(StepContribution contribution, Chunk<I> chunk) throws SkipOverflowException, Exception {
		return doRead();
	}

    @Nullable
	protected final I doRead() throws Exception {
		try {
			listener.beforeRead();
			I item = itemReader.read(); AbstractPagingItemReader doRead 메소드 호출 
			if(item != null) {
				listener.afterRead(item);
			}
			return item;
		}
		catch (Exception e) {
			if (logger.isDebugEnabled()) {
				logger.debug(e.getMessage() + " : " + e.getClass().getName());
			}
			listener.onReadError(e);
			throw e;
		}
	}

    ...

}

```

```java

public abstract class AbstractPagingItemReader<T> extends AbstractItemCountingItemStreamItemReader<T> 
        implements InitializingBean {

@Override
	protected T doRead() throws Exception {

		synchronized (lock) {

			if (results == null || current >= pageSize) {

				if (logger.isDebugEnabled()) {
					logger.debug("Reading page " + getPage());
				}

				doReadPage(); //밑에 jpaPagingItemReader가 이클래스를 상속해서 doReadPage를 구현했음.
				page++;
				if (current >= pageSize) {
					current = 0;
				}

			}

			int next = current++;
			if (next < results.size()) {
				return results.get(next);
			}
			else {
				return null;
			}

		}

	}
}
```


```java
public class JpaPagingItemReader<T> extends AbstractPagingItemReader<T> {


    @Override
	@SuppressWarnings("unchecked")
	protected void doReadPage() {

		EntityTransaction tx = null;
		
		if (transacted) {
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

}
```