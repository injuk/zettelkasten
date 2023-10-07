Kotlin의 경우, TransactionTemplate의 execute 메소드가 Nullable로 추론되는 점에 주의하자.
```
/**  
 * @Component로 등록시 helper를 주입받아 execute { ... } 형태로 사용 가능  
 */
class TransactionHelper(  
    private val txManager: PlatformTransactionManager,  
) {  
    private val txTemplate: TransactionTemplate by lazy { TransactionTemplate(txManager) }  
  
    fun <T> execute(propagation: Propagation = Propagation.REQUIRED, block: () -> T): T?  
        = txTemplate  
            .apply { propagationBehavior = propagation.behavior }  
            .execute<T> { block() }  
  
    enum class Propagation(val behavior: Int) {  
        REQUIRED(0),  
        SUPPORTS(1),  
        MANDATORY(2),  
  
        REQUIRES_NEW(3),  
        NOT_SUPPORTED(4),  
        NEVER(5),  
        NESTED(6),  
    }  
}
```