#스프링
참고: [[Spring 트랜잭션 관리]]
스프링은 다음과 같은 두 가지 트랜잭션 제어 방식을 지원한다
* 선언적 트랜잭션 -> `@Transactional` 어노테이션을 명시하는 방식
* 프로그래밍적 트랜잭션 -> `PlatformTransactionManager` 또는 `TransactionTemplate`을 사용하는 방식
	* Spring 팀의 경우, 두 방식 중에서는 `TransactionTemplate`을 권장한다고 함... 왤까
선언적 트랜잭션과 프로그래밍적 트랜잭션은 우열이 따로 있진 않는 듯?