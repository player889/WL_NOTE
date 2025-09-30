@TransactionalEventListener

現在我們做一個總結，如果你遇到這樣的業務，操作B需要在操作A事務提交後去執行，那麼TransactionalEventListener是一個很好地選擇。這裡需要**特別注意**的一個點就是：當B操作有資料改動並持久化時，並希望在A操作的AFTER_COMMIT階段執行，那麼你需要將B事務聲明為**PROPAGATION_REQUIRES_NEW**。這是因為A操作的事務提交後，事務資源可能仍然處於啟動狀態，如果B操作使用默認的**PROPAGATION_REQUIRED**的話，會直接加入到操作A的事務中，但是這時候事務A是不會再提交，結果就是程序寫了修改和保存邏輯，但是資料庫資料卻沒有發生變化，解決方案就是要明確的將操作B的事務設為**PROPAGATION_REQUIRES_NEW**。
https://juejin.cn/post/7011685509567086606

* [@TransactionalEventListener的使用和实现原理](https://blog.csdn.net/qq_41378597/article/details/105748703)
* [@EventListener注解的使用](https://blog.csdn.net/qq_16992475/article/details/122570211)