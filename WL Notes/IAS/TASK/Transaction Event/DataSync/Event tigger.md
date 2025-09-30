```java
protected void publishChangeEventAfterCommit(final Account model) {
    final Runnable task = () - > eventPublisher.publishEvent(new AccountChangeEvent(model));
    if (TransactionSynchronizationManager.isActualTransactionActive()) {
        TransactionSynchronizationManager.registerSynchronization(
            new TransactionSynchronizationAdapter() {
                @Override
                public void afterCommit() {
                    task.run();
                }
            });
    } else {
        task.run();
    }
}
```