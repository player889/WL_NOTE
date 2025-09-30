Loop org to execute:

- Loop currency
- Loop Customer
- Loop Account
- Loop Card

```java
@SneakyThrows
public List<com.worldline.cardlite.ias.entity.Account> runSyncForOrg(final Organization org, final boolean skipCurrSync, final boolean forceUpdate, final boolean atomicCommit, final boolean forceOrgDataClone) {
    if (!skipCurrSync) runInTransaction((txSts) -> syncCurrency(forceUpdate));
    runInTransaction((txSts) -> syncOrgByOrgId(org.getId(), forceUpdate, forceOrgDataClone));
    runInTransaction((txSts) -> syncCurrencyRateByOrgId(org.getId(), forceUpdate));
    runInTransaction((txSts) -> syncCustomerByOrgId(org.getId(), forceUpdate, atomicCommit));
    // XXX: Could be null for shadow account, we should filter it.
    val ret = runInTransaction((txSts) -> syncAccountByOrgId(org.getId(), forceUpdate, atomicCommit))
        .stream().filter(it -> it != null).collect(Collectors.toList());;
    runInTransaction((txSts) -> syncCardByOrgId(org.getId(), forceUpdate, atomicCommit));
    return ret;
}

```

SYNC CUSTOMER

---

ISSUE

- IAS 卡戶人資訊 ， 同步資訊 也不清楚。 ==> new IBO timestamep vs IAS timestamp
- IBO API --> new IAS GUI


![[2024-08-13_115828.png]]