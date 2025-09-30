1. Get org from CC
    1. syncCurrency
    2. syncOrgByOrgId
    3. syncCurrencyRateByOrgId
    4. syncCustomerByOrgId
    5. syncAccountByOrgId
    6. syncCardByOrgId
2. Org

```java
return validateSyncLog(
	syncLogKey,
	remoteVersion,
	OrgSyncHandler.ORG_MAPPER_VERSION,
	(syncLog) -> {
    return orgSyncHandler.processSyncEvent(
	    new DataChangeEvent<>(DataChangeType.CREATE, sourceEntityMapper.mapOrganization(srcOrg)), forceOrgDataClone);
	},
	srcOrg,
	payload,
	flag
);
```

```java
public Institution createOrUpdateInstitutionEntity(final SourceOrgEntity srcOrg, final boolean forceOrgDataClone) {
	val orgCode = StringUtils.trimToNull(srcOrg.getOrgCode());
	Preconditions.checkNotNull(orgCode, String.format("The 'orgCode' property from 'SourceOrgEntity' (%s) must not be null.", srcOrg.getId()));

	final DataCache dataCache = new DataCache();

//        var ret = institutionDao.getEntity(orgCode);
	final String extRefKey = DataSyncRefUtils.getExtRefKeyForOrg(srcOrg.getId().toString());
	var ret = institutionDao.findOneByExtRefKey(extRefKey).orElse(null);
	final boolean forUpdate;
	if (ret == null) {
		forUpdate = false;
		ret = new Institution();
		ret.setItnIdnCde(generateNewOrgCode(orgCode));
		ret.setExtRefKey(extRefKey);
//            ret.setPdtIdnCde(srcProd.getProductCode());
		// TODO: create new Institution record.

		// Copy from one of the existing product record.
//            val ref = findSimilarInstitution(TEMPLATE_INSTITUTION_ID);
		val ref = institutionDao.requireEntity(TEMPLATE_ITN_IDN_CDE);
		if (ref != null) {
			BeanUtils.copyProperties(ref, ret, "itnIdnCde", "extRefKey", "nmeTxt", "_thisEntity");
		}
	} else {
		forUpdate = true;
		EntityUtils.initEntityForUpdate(ret);
	}
//        ret.setChgSts("1");
	ret.setNmeTxt(String.format("%s - %s", orgCode, srcOrg.getOrgName()));
	ret.setAtvInd("1");
	// TODO: update the fields of existing Production record.
	ret.setLstUpdUid(DATA_SYNC_SYS_USER_ID);
	if (forUpdate) {
		institutionDao.updateEntity(ret);
	} else {
		institutionDao.saveEntity(ret);
	}
	dataCache.flushEntityCache(daoProvider);

	// check with the flag forceOrgDataClone
	if (!forUpdate || forceOrgDataClone) {
		// clone data from template (WLCN)
		replicateOrgDataFromTemplate(dataCache, ret, institutionDao.requireEntity(TEMPLATE_ITN_IDN_CDE));
	}
	dataCache.flushEntityCache(daoProvider);
	return ret;
}
```

3. Customer #公司卡

```java
protected CustomerMeta processSyncEvent(
    /*final com.worldline.optimus.ibo.model.Customer srcCust,*/
    final SourceCustomerEntity srcCust,
    final DataChangeType changeType
) {
    // Lookup institution entity.
    val orgExtRefKey = DataSyncRefUtils.getExtRefKeyForOrg(Optional.ofNullable(srcCust).map(it -> it.getOrganizationId()).map(it -> it.toString()).get());
    val institution = institutionDao.findOneByExtRefKey(orgExtRefKey).orElseThrow(
        () -> new IllegalStateException(String.format("Unable to find institution by extRefKey: %s", orgExtRefKey))
    );
    val institutionId = institution.getItnIdnCde();
    if (CustomerType.ORDINARY.equals(srcCust.getCustomerType())) { //個人卡
        return createOrUpdateOrdinaryCustomer(institutionId, srcCust);
    } else if (CustomerType.CORPORATE.equals(srcCust.getCustomerType())) {  //公司卡
        return createOrUpdateCorporateCustomer(institutionId, srcCust);
    } else {
        throw new IllegalArgumentException("Unsupported customer type: " + srcCust.getCustomerType());
    }
}
```

---

**`DATA_SYNC_LOG`** table

**isRecordChange**

```java
public Optional<DataSyncRecordState>  isRecordChanged(final DataSyncLogKey key, final String remoteVersion) {
    val log = dataSyncLogMapper.getEntity(key);
    if (log == null) {
        return Optional.empty();
    }
    val logVersion = log.getRemoteVersion();
    val changed = ObjectUtils.compare(remoteVersion, logVersion, false) > 0;
    return Optional.of(new DataSyncRecordState(log, changed));
}
```
