
```java
@ExceptionHandler(value = { HttpServerException.class })
@ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)
@ResponseBody
protected ResponseEntity<Object> handleServerException(HttpServerException ex, WebRequest request) {

	if(ex.getLogLevel() == null || ex.getLogLevel().equals("error")){
		logger.error("500 Status Code", ex);
	}else if(ex.getLogLevel().equals("info")){
		logger.info("500 Status Code", ex);
	}else{
		logger.debug("500 Status Code", ex);
	}

final ExceptionResponseBody bodyOfResponse = new ExceptionResponseBody(ex.getMessage(), HttpStatus.INTERNAL_SERVER_ERROR.value());
return new ResponseEntity<Object>(bodyOfResponse, new HttpHeaders(), HttpStatus.INTERNAL_SERVER_ERROR);
}

```

HttpServerException안에다가 json object를 주고 이게 널이아니면 에러메시지로 그 json object를 던지면 될것같다.
