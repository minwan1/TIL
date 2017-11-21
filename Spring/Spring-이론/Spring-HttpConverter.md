

```java
public class SpringfoxJsonToGsonAdapter implements JsonSerializer<Json> {

    @Override
    public JsonElement serialize(Json json, Type type, JsonSerializationContext context) {
        final JsonParser parser = new JsonParser();
        return parser.parse(json.value());
    }

}


```


```java
@Configuration
public class DispatcherConfig extends WebMvcConfigurerAdapter {
   @Bean
      public GsonHttpMessageConverter gsonHttpMessageConverter() {
          GsonHttpMessageConverter converter = new GsonHttpMessageConverter();
          converter.setGson(gson());
          return converter;
      }

      private Gson gson() {
          final GsonBuilder builder = new GsonBuilder();
          builder.registerTypeAdapter(Json.class, new SpringfoxJsonToGsonAdapter());
          return builder.excludeFieldsWithoutExposeAnnotation().create();
      }


      @Override
  public void configureMessageConverters(List<HttpMessageConverter<?>> converters) {
      super.configureMessageConverters(converters);
      converters.add(gsonHttpMessageConverter());

  }
}
```


[gson스웨거랑같이쓰기](https://stackoverflow.com/questions/30219946/springfoxswagger2-does-not-work-with-gsonhttpmessageconverterconfig)
