# Mockito 사용시 void method mocking 방법 with Spring 예제

## 문제

다음과 같이 반환타입이 void인 간단한 메서드가 있다고 가정하겠습니다.

```java
@Transactional
public void deletePost(Long id) {
    postRepository.deleteById(id);
}
```

반환타입이 void인 메서드는 Mockito에서 when(), given()을 이용해서 테스트할 수 없습니다.

## doAnswer()와 doThrow()를 이용한 테스트

when() 또는 given() 대신 doAnswer() 와 doThrow() 를 사용할 수 있습니다.

### doAnswer()

doAnswer()를 사용하는 경우 Answer 인터페이스를 인수로 받습니다. 찾아본 예제들은 대부분 화면에 값을 출력하는데 사용하여, 아래에서는 바로 null을 반환했습니다.

```java
@Test
void requestDeletePostAndReturn204WithNothing() throws Exception {

    var targetPostId = 1L;

    doAnswer(invocation -> null)
            .when(postService).deletePost(anyLong());

    mockMvc.perform(delete("/api/v1/posts/" + targetPostId))
            .andExpect(status().isNoContent());

}
```

아래는 [공식문서](https://javadoc.io/static/org.mockito/mockito-core/5.6.0/org/mockito/Mockito.html#doAnswer(org.mockito.stubbing.Answer)) 예제입니다.

```java
  doAnswer(new Answer() {
      public Object answer(InvocationOnMock invocation) {
          Object[] args = invocation.getArguments();
          Mock mock = invocation.getMock();
          return null;
      }})
  .when(mock).someMethod();
```

### doThrow()

doThrow()는 Throwable을 인수로 받습니다. 필요에 따라 추가하면 됩니다. 

```java
@Test
void requestDeletePostAndReturn404() throws Exception {

    var targetPostId = 999L;

    doThrow(new PostNotFoundException(targetPostId))
            .when(postService).deletePost(anyLong());

    mockMvc.perform(delete("/api/v1/posts/" + targetPostId))
            .andExpect(status().isNotFound())
            .andExpect(content().string(containsString(new PostNotFoundException(targetPostId).getMessage())));

}
```

아래는 [공식문서](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html#doThrow(java.lang.Throwable...)) 예제입니다.

```java
doThrow(new RuntimeException()).when(mock).someVoidMethod();
doThrow(RuntimeException.class).when(mock).someVoidMethod();
doThrow(RuntimeException.class, BigFailure.class).when(mock).someVoidMethod();
```


이외에도 doNothing(), doCallRealMethod() 등이 있습니다.

출처: [Mock Void method with Mockito](https://stacktraceguru.com/unittest/mock-void-method)