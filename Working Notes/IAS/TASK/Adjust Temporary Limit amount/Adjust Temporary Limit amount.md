https://confluence.worldline.com/display/OP/Temporary+Limit+Adjustment

4999990000000002

## Query limit adjust by type and card number from GUI

http://localhost:8097/api/account/requestSync/1D5B9A78-3B00-40A0-A87C-836678274998

![[Pasted image 20230609101118.png]]

Revert Processing

```
7 DeleteTriggerBatchWriter.java
6 CreditAdjustRevertStep.java
5 CreditAdjustRevertPrepare.java
4 creditAdjustRevert.json
3 CreditAdjustRevertProcess.java
3 TriggerBatchRecordListReader.java
2 job_creditAdjustRevert.xml
1 batch_job.xml
0 CreditAdjustRevertJob.java
```

Setup Processing

```
9 DeleteTriggerBatchWriter.java
8 CreditAdjustSetupStep.java
7 CreditAdjustSetupPrepare.java
6 creditAdjustSetup.json
5 CreditAdjustSetupProcess.java
4 TriggerBatchRecordListReader.java
3 CreditAdjustSetupJob.java
2 job_creditAdjustSetup.xml
1 batch_job.xml
```

Create a trigger data in trigger_table

```
process_4 AccountCreditDataSyncHelper.java
process_3 CreditAdjustmentProcess.java
process_2 CreditAdjustmentPrepare.java
process_1 CreditAdjustmentParamCheck.java
SyncAgentController.java
```

IAS adjust limit GUI

```
CreditAdjustProcessImpl.java
CreditAdjustmentProcess.java
```

---

```JAVA
@Bean(name = "dataSyncJob")
public Job dataSyncJob() {
	return jobBuilderFactory.get("dataSyncJob")
			.start(stepBuilderFactory.get("dataSyncJob.step1")
					.chunk(200)
					.reader(sourceCustomerReader)
					.writer((ItemWriter) dataSyncCustomerWriter)
					.build())
			.next(stepBuilderFactory.get("dataSyncJob.step2")
					.chunk(200)
					.reader(sourceAccountReader)
					.writer((ItemWriter) dataSyncAccountWriter)
					.faultTolerant().skipPolicy(dataSyncSkipPolicy)
					.retryLimit(0)
					.build())
			.build();
}
```

![[Pasted image 20230712164553.png|800]]
