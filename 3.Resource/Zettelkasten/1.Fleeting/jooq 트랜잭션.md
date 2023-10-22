#트러블슈팅 

JooqConfiguration을 만들고 별 짓 다했는데, 알고보니 `spring-boot-starter-jooq` 를 사용중이면 
그냥 대부분의 필요한 내용을 알아서 설정해준다고 함
간혹 TransactionProvider나 TransactionAware 뭐시기를 설정하라는 글이 있는데, 그건 Spring Boot와 위 라이브러리 없이 깡 Spring을 쓰는 경우에 필요하다
확인해보니 `TransactionTemplate`, `@Transactional` 다 사용 가능했음!

### 참고
* [Using Jooq](https://docs.spring.io/spring-boot/docs/1.3.5.RELEASE/reference/html/boot-features-jooq.html)
* [StackOverflow 참고 자료](https://stackoverflow.com/questions/39985645/jooq-and-spring-transaction-management)
* 