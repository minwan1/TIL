Java에는 Primitive Type을 Reference Type으로 사용하기 위해서 만든 Wrapper Class가 있습니다.


Primitive Type	Wrapper Class
byte	Byte
short	Short
int	Integer
long	Long
float	Float
double	Double
char	Character
boolean	Boolean
void	Void


```java
...
  /**
    * Cache to support the object identity semantics of autoboxing for values between
    * -128 and 127 (inclusive) as required by JLS.
    *
    * The cache is initialized on first usage.  The size of the cache
    * may be controlled by the {@code -XX:AutoBoxCacheMax=<size>} option.
    * During VM initialization, java.lang.Integer.IntegerCache.high property
    * may be set and saved in the private system properties in the
    * sun.misc.VM class.
    */
  private static class IntegerCache {
          static final int low = -128;
          static final int high;
          static final Integer cache[];
  ​
          static {
              // high value may be configured by property
              int h = 127;
              String integerCacheHighPropValue =
                  sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
              if (integerCacheHighPropValue != null) {
                  try {
                      int i = parseInt(integerCacheHighPropValue);
                      i = Math.max(i, 127);
                      // Maximum array size is Integer.MAX_VALUE
                      h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
                  } catch( NumberFormatException nfe) {
                      // If the property cannot be parsed into an int, ignore it.
                  }
              }
              high = h;
  ​
              cache = new Integer[(high - low) + 1];
              int j = low;
              for(int k = 0; k < cache.length; k++)
                  cache[k] = new Integer(j++);
  ​
              // range [-128, 127] must be interned (JLS7 5.1.7)
              assert IntegerCache.high >= 127;
          }
  ​
          private IntegerCache() {}
      }
  ​
  ...



출처: http://feco.tistory.com/112 [wmJun]
```

또한, valueOf 메소드를 봐도 캐싱된 값을 사용하는 것을 알 수 있으며, 반드시 새 인스턴스가 필요한 것이 아니라면 valueOf 를 이용해서 인스턴스를 할당받는 것을 권장합니다.


Integer class 는 클래스 로드시 -128~127 값에 대한 인스턴스를 미리 생성해놓고 오토박싱시에 이 범위의 값이면 미리 생성된 인스턴스를 리턴합니다. 그래서 캐싱 범위에 속하는 1에 대해서는 미리 생성된 같은 인스턴스를 가지게 되는 것이고, 캐싱 범위 밖인 300에 대해서는 각각 새로운 인스턴스를 받게 되는 것이지요.


왜냐하면, 위와 같이 생성자를 통한 객체 생성에서는 캐싱된 값을 사용하고 있지 않고 있기 때문이지요.



당연히, Integer Class뿐만 아니라 Byte, Short, Long, Character 역시 자체적으로 캐싱을 하고 있습니다.
Float나 Double에서 쓰지 않는 것은, 자주 쓰는 범위를 예측하기에는 너무 어렵고, 그 사이에 너무 많은 값이 존재할 수 있어서 일까요?



출처: http://feco.tistory.com/112 [wmJun]
출처: http://feco.tistory.com/112 [wmJun]
