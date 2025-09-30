```java
@GetMapping(path = "/account/requestSync/{id}", produces = "application/json")
@ResponseBody
public String requestSyncAccount(@PathVariable("id") final String id0)
	throws RemoteException, DecoderException {
	final UUID id = parseUuidFromString(id0);
	final com.worldline.optimus.ibo.model.Account srcAcct = accountRepositoryApi.findOneAccount(id);
	Preconditions.checkNotNull(srcAcct, String.format("Source Account cannot be found by ID: %s", id0));
	return syncAgentService.requestSyncAccount(srcAcct).getActIdnSky();
}
```

```java
@SneakyThrows
@Transactional(isolation = Isolation.READ_COMMITTED)
public Account requestSyncAccount(final com.worldline.optimus.ibo.model.Account srcAcct) throws DataAlreadySynchronizedException {
	return requestSyncAccount(srcAcct, true);
}
```

```java
@SneakyThrows
@Transactional(isolation = Isolation.READ_COMMITTED)
public Account requestSyncAccount(final com.worldline.optimus.ibo.model.Account srcAcct, final boolean forceUpdate) throws DataAlreadySynchronizedException {
	val flag = forceUpdate ?
		DataAlreadySynchronizedBehavior.PROCEED_ANYWAY : DataAlreadySynchronizedBehavior.IGNORE_BY_RETURN_NULL;
	return requestSyncAccount(srcAcct, flag);
}
```

```java
@SneakyThrows
@Transactional(isolation = Isolation.READ_COMMITTED)
public Account requestSyncAccount(final com.worldline.optimus.ibo.model.Account srcAcct, final DataAlreadySynchronizedBehavior flag) throws DataAlreadySynchronizedException {
	val remoteVersion = Long.toString(srcAcct.getLastModifiedDate().atZone(ZoneId.systemDefault()).toInstant().toEpochMilli());
	val syncLogKey = new DataSyncLogKey(DataSyncAgentConstants.SCOPE_ID,com.worldline.optimus.ibo.model.Account.class.getSimpleName(), srcAcct.getAccountNumber());
	val payload = objectMapper.writeValueAsString(srcAcct);
	return validateSyncLog(
		syncLogKey,
		remoteVersion,
		AccountSyncHandler.ACCOUNT_MAPPER_VERSION, 
		(syncLog) -> accountSyncHandler.processSyncEvent(new DataChangeEvent<>(DataChangeType.CREATE, sourceEntityMapper.mapAccount(srcAcct))), 
		srcAcct, payload, flag
	);
}
```

```java
protected <R> R validateSyncLog(
		@NotNull final DataSyncLogKey syncLogKey,
		final String remoteVersion,
		final int mapperVersion,
		@NotNull Function<DataSyncLog, R> processor,
		@Nullable final Object remoteObject,
		@Nullable final String payload,
		@NotNull final DataAlreadySynchronizedBehavior dataAlreadySynchronizedBehavior
) {
	val optState = dataSyncStateManager.isRecordChanged(syncLogKey, remoteVersion);
	var syncLog = optState.map(it -> it.getLog()).orElse(null);
	if (optState.isPresent()) {
		if (!optState.get().isChanged()) {
			val errmsg = String.format(
					"The local record (%s) is newer (%s) or already synchronized with remote version (%s), "
							+ "and the syncDefVersion (%s) is also newer than expected (%s).",
					syncLogKey, syncLog.getRemoteVersion(), remoteVersion, syncLog.getSyncDefVersion(),
					mapperVersion);
			LOGGER.warn(errmsg);
			if (DataAlreadySynchronizedBehavior.THROW_ERROR.equals(dataAlreadySynchronizedBehavior)) {
				throw new DataAlreadySynchronizedException(errmsg);
			} else if (DataAlreadySynchronizedBehavior.IGNORE_BY_RETURN_NULL.equals(dataAlreadySynchronizedBehavior)) {
				LOGGER.debug(String.format("%s is set to %s", DataAlreadySynchronizedBehavior.class.getSimpleName(),
						dataAlreadySynchronizedBehavior));
				return null;
			} else /*if (DataAlreadySynchronizedBehavior.PROCEED_ANYWAY.equals(dataAlreadySynchronizedBehavior))*/ {
				// proceed anyway.
			}
		}

		// Extract the changes between old and new records.
		if (remoteObject != null) {
			val changes = dataSyncStateManager.computeChangedFields(optState.get(),
					remoteObject, remoteObject.getClass());
			val changelog = new DataSyncChangeLog(new DataSyncRequestKey(UUID.randomUUID().toString()),
					syncLogKey, new Date(), "STARTED", new Date(), null,
					changes.getDiff().prettyPrint(), new Date(), 1L);
			dataSyncChangeLogMapper.saveEntity(changelog);
		}
	}
	val isUpdate = syncLog != null;
	val ret = processor.apply(syncLog);
	if (syncLog == null) {
		syncLog = new DataSyncLog();
		syncLog.setId(syncLogKey);
	}
	syncLog.setPayload(payload);
	syncLog.setLastUpdatedDate(new Date());
	syncLog.setRemoteVersion(remoteVersion);
	syncLog.setSyncDefVersion(Integer.toString(mapperVersion));

	if (isUpdate) {
		dataSyncLogMapper.updateEntity(syncLog);
	} else {
		dataSyncLogMapper.saveEntity(syncLog);
	}
	return ret;
}
```

```java
@Override
public Optional<DataSyncRecordState> isRecordChanged(final DataSyncLogKey key, final String remoteVersion) {
	val log = dataSyncLogMapper.getEntity(key);
	if (log == null) {
		return Optional.empty();
	}
	val logVersion = log.getRemoteVersion();
	val changed = ObjectUtils.compare(remoteVersion, logVersion, false) > 0;
	return Optional.of(new DataSyncRecordState(log, changed));
}
```
- [用JaVers比較對象](http://qinghua.github.io/javers/)
- [DDD中Diff的應用(JAVERS)的封裝](https://blog.csdn.net/qq_42651904/article/details/120110072)
```java
@SneakyThrows
@Override
public ChangedInfo computeChangedFields(final DataSyncRecordState state, final Object remoteRecord, final Class<?> objectType) {
	final Object oriObj = objectMapper.readValue(state.getLog().getPayload(), objectType);
	// @formatter:off
	final Javers javers = JaversBuilder.javers()
			.registerEntity(new EntityDefinition(CrudTransferObject.class, "id"))
			.build();
	// @formatter:on
	try {
		final Diff diff = javers.compare(oriObj, remoteRecord);
		List<ChangedField> changeFieldList = diff.getChanges().getChangesByType(PropertyChange.class).stream().map(it -> new ChangedField(it.getPropertyName(), it.getLeft(), it.getRight())).collect(Collectors.toList());
		return new ChangedInfo(diff, changeFieldList);
	} catch (Throwable ex) {
		LOGGER.error("Error", ex);
		throw ex;
}
```

```ad-note
title: JaVers
~~~java

final Diff diff = javers.compare(oriObj, remoteRecord);
List<ChangedField> changeFieldList = diff.getChanges().getChangesByType(PropertyChange.class)
.stream().map(it -> new ChangedField(it.getPropertyName(), it.getLeft(),it.getRight())
).collect(Collectors.toList());
~~~

```

```java
public Account processSyncEvent(final DataChangeEvent<SourceAccountEntity> chgEvent) {
	return processSyncEvent(chgEvent.getPayload(), chgEvent.getEventType());
}
```

```java
@SneakyThrows
protected Account processSyncEvent(final SourceAccountEntity srcAccount, final DataChangeType changeType) {
	val dataCache = new DataCache();
	val acctActRfeNbrTxt = DataSyncRefUtils.getActRfeNbrTxtForAcct(institutionId, srcAccount.getAccountNumber());
	val destAccts = accountMapper.getAccountByActRfeNbrTxt(acctActRfeNbrTxt);
	Preconditions.checkState(destAccts.size() <= 1,
			String.format("There are multiple Account (%d) by actRfeNbrTxt: %s",
					destAccts.size(), srcAccount.getId().toString()));

	/**
	 * FIXME: Skip synchronize for Shadow Account for now.
	 */
	val shadowAccts = shadowAccountRepositoryApi.listShadowAccount(new RestListRequestBody(
			new RestFilter(new RestCriteria[] {new RestCriteria(Operator.EQ, "shadowAccount.id", srcAccount.getId())}),
			new RestSort()
	));
	if (!shadowAccts.isEmpty()) {
		LOGGER.warn(String.format("Skipping synchronize for Shadow Account (%s / %s) for now...",
				srcAccount.getAccountNumber(), srcAccount.getId()));
		return null;
	}


	/**
	 * FIXME: Dependent model check before synchronization.
	 *
	 * There are chances that the requesting model might have dependent models which are not
	 * being synchronized yet. In this case, we should either synchronize them together, or
	 * request synchronization of missing model and push-back the current sync task.
	 */

	Account destAcct;
	final boolean update;
	if (!destAccts.isEmpty()) {
		destAcct = destAccts.stream().findFirst().get();
		update = true;
	} else {
		destAcct = new Account();
		destAcct.setItnIdnCde(institutionId);
		destAcct.setActIdnSky(GenerateSystemKey.getPriKey(GEN_ACCOUNT_ID, 16));
		destAcct.setActRfeNbrTxt(acctActRfeNbrTxt);
		AccountSyncHelper.initializeAccountDefault(destAcct);
		update = false;
	}
	processUpdateAccount(dataCache, srcAccount, destAcct);

	if (update) {
		destAcct.preUpdate();
//            accountMapper.updateEntitySelective(destAcct);
		accountDao.updateEntitySelective(destAcct);
	} else {
		destAcct.preInsert();
//            accountMapper.saveEntity(destAcct);
		accountDao.saveEntity(destAcct);
	}

	// TODO: Update PTOP and Customer Account with proper structure.
	val custAcctAndStru = processUpdateAccountStructure(dataCache, srcAccount, destAcct);

	val accountCreditDataSyncHelper = new AccountCreditDataSyncHelper(ACCOUNT_MAPPER_VERSION, accountManagerImpl,
			accountCreditDataDao, dataSyncStateManager);
	// Update of customer account credit balance.
	if (custAcctAndStru != null && custAcctAndStru.getCustAcct() != null) {
		accountCreditDataSyncHelper.handleAccountCreditData(dataCache, custAcctAndStru.getCustAcct(),
			srcAccount.getCustomer().getCustomerCreditBalance(), srcAccount.getCustomer().getCustomerNumber());
	}
	accountCreditDataSyncHelper.handleAccountCreditData(dataCache, destAcct, srcAccount);

	// Flush delay entities.
	dataCache.flushEntityCache(DaoProvider.instance());

	return destAcct;
}

protected DateTime getOpenDate() {
	return new DateTime(2020, 1, 1, 0, 0);
}

protected void processUpdateAccount(@NotNull final IDataCache dataCache,
		@NotNull final SourceAccountEntity srcAccount,
		@NotNull final Account destAcct) {
	val srcCust = srcAccount.getCustomer();
	if (srcCust != null && srcCust.getId() != null) {
		val destCustMeta = customerSyncHandler.createOrUpdateCustomer(srcCust);
		destAcct.setCsrIdnSky(destCustMeta.getCustomer().getCsrIdnSky());
		destAcct.setCttIdnSky(destCustMeta.getContact().stream().findFirst().map(it -> it.getCttIdnSky()).orElse(null));
	}
	/**
	 * FIXME: Update the Account fields.
	 */
	val srcProduct = srcAccount.getProduct();
//        val destProd = productSyncHandler.getProductOrDefault(destAcct.getItnIdnCde(), srcProduct);
	val destProd = productSyncHandler.createOrUpdateProductEntity(destAcct.getItnIdnCde(), srcProduct);
	val destCurr = getCurrencyOrError(srcProduct.getCurrency());
	destAcct.setCryCde(destCurr.getCryCde());
	destAcct.setPdtIdnCde(destProd.getPdtIdnCde());
	destAcct.setActTypIdnCde(ACCT_TYPE_PRIM_ACT);

	// FIXME: Override account open date.
	destAcct.setOpeDte(DateHelper.formatDate(new Date(getOpenDate().toDate().getTime())));
	if (StringUtils.isBlank(destAcct.getLstCleDte())) {
		destAcct.setLstCleDte(destAcct.getOpeDte());
	}

	/**
	 * TODO: Initialize `cpeActIdn` of account.
	 * This is referred to 账户属性（贷记卡/公司卡/准贷记卡/借记卡）.
	 */
//        final IDataCache dataCache = getDataCache();
//        final String productId = context.getAttributesString(ACCOUNT_PRODUCT_CODE);
//        final String accountType = context.getAttributesString(ACCOUNT_TYPE_CODE);
	val productId = destProd.getPdtIdnCde();
	final String actTypIdnCde = destAcct.getActTypIdnCde();
	IProductAccountConfigBo configBo = accountManagerImpl.getProductAccountConfigBo(productId, actTypIdnCde, dataCache);
	destAcct.initCpeActIdn(configBo.getProduct().getPdtTypCde());
	handleAccountStatusUpdate(dataCache, destAcct, srcAccount);

	destAcct.setLstUpdUid(DATA_SYNC_SYS_USER_ID);

	/**
	 * FIXME: Consider where should we handle the update of credit limit data during account sync.
	 */
}
```

```java
public static String getActRfeNbrTxtForAcct(final String itnCdnCde, final String accountNumber) {
	return String.format("acct_%s_%s", itnCdnCde, accountNumber);
}
```

```java
protected void handleAccountStatusUpdate(
		@NotNull final IDataCache dataCache,
		@NotNull final Account destAcct,
		@NotNull final SourceAccountEntity srcAcct) {
	val srcSts = srcAcct.getAccountStatus();
	Preconditions.checkNotNull(srcSts, String.format("Status Code of Source Account (%s) cannot be null.",
			srcAcct.getAccountNumber()));
	val acctStatusCodeMapper = statusCodeMapperFactory.getAccountStatusCodeMapper();
	val resolvedCode = acctStatusCodeMapper.mapToResolved(srcSts.name());
	Preconditions.checkNotNull(resolvedCode, String.format("Unable to resolve account status code (%s) for account: %s",
			srcSts, srcAcct.getAccountNumber()));
	if (!StringUtils.equalsIgnoreCase(resolvedCode.getStsCde(), destAcct.getMnlStsCde())) {
		// status code updated.
		destAcct.setMnlStsCde(resolvedCode.getStsCde());
		destAcct.setMnlStsSetDte(DateUtil.getFormatDate(DateUtil.now()));
	}
}
```
```xml
==============
AccountMapper.xml
<select id="getAccountByActRfeNbrTxt" parameterType="String" resultMap="BaseResultMap">
	select acc.* from account acc
	where acc.act_rfe_nbr_txt = #{actRfeNbrTxt,jdbcType=CHAR}
</select>
```


