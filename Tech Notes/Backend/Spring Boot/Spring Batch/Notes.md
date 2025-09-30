| Batch Basic Components | 描述                |
| ---------------------- | ------------------- |
| transactionManager     | 事務管理器<br/>     |
| JobRepository          | 任務持久化操作<br/> |
| JobBuilderFactory      | 任務創建工廠<br/>   |
| StepBuilderFactory     | 任務步創建工廠<br/> |
| JobLauncher            | 任務啟動器          |

| 領域對象      | 描述                                                  |
| ------------- | ----------------------------------------------------- |
| JobRepository | 作業倉庫，保存Job、Step執行過程中的狀態及結果         |
| JobLauncher   | 作業執行器，是執行Job的入口                           |
| Job           | 壹個批處理任務，由壹個或多個Step組成                  |
| Step          | 壹個任務的具體的執行邏輯單位                          |
| Item          | 壹條數據記錄                                          |
| ItemReader    | 從數據源讀數據<br/>                                   |
| ItemProcessor | 對數據進行處理，如數據清洗、轉換、過濾、校驗等        |
| ItemWriter    | 寫入數據到指定目標                                    |
| Chunk<br/>    | 給定數量的Item集合，如讀取到chunk數量後，才進行寫操作 |
| Tasklet       | Step中具體執行邏輯，可重復執行                        |

### JobExecution

| 屬性              | 說明                                                         |
| ----------------- | ------------------------------------------------------------ |
| status            | 狀態類名為BatchStatus，它指示了執行的狀態。<br />在執行的過程中狀態為BatchStatus#STARTED<br />失敗：BatchStatus#FAILED，<br />完成：BatchStatus#COMPLETED |
| startTime         | java.util.Date對象，標記批處理任務啟動的系統時間，批處理任務未啟動數據為空 |
| endTime           | java.util.Date對象，結束時間無論是否成功都包含該數據，如未處理完為空 |
| exitStatus        | ExitStatus類，記錄運行結果。                                 |
| createTime        | java.util.Date,JobExecution的創建時間，某些使用execution已經創建但是並未開始運行。 |
| lastUpdate        | java.util.Date，最後壹次更新時間                             |
| executionContext  | 批處理任務執行的所有用戶數據                                 |
| failureExceptions | 記錄在執行Job時的異常，對於排查問題非常有用                  |

### StepExecution

| 属性             | 说明                                                         |
| ---------------- | ------------------------------------------------------------ |
| status           | 狀態類名為BatchStatus，它指示了執行的狀態。<br />開始：BatchStatus#STARTED<br />失敗：BatchStatus#FAILED<br />完成：BatchStatus#COMPLETED |
| startTime        | java.util.Date對象，標記StepExecution啟動的系統時間，未啟動數據為空 |
| endTime          | java.util.Date對象，結束時間，無論是否成功都包含該數據，如未處理完為空 |
| exitStatus       | ExitStatus類，記錄運行結果。                                 |
| createTime       | java.util.Date,JobExecution的創建時間，某些使用execution已經創建但是並未開始運行。 |
| lastUpdate       | java.util.Date，最後壹次更新時間                             |
| executionContext | 批處理任務執行的所有用戶數據                                 |
| readCount        | 成功讀取數據的次數                                           |
| wirteCount       | 成功寫入數據的次數                                           |
| commitCount      | 成功提交數據的次數                                           |
| rollbackCount    | 回歸數據的次數，有業務代碼觸發                               |
| readSkipCount    | 當讀數據發生錯誤時跳過處理的次數                             |
| processSkipCount | 當處理過程發生錯誤，跳過處理的次數                           |
| filterCount      | 被過濾規則攔截未處理的次數                                   |

| 表名                         | 作用                     |
| ---------------------------- | ------------------------ |
| batch_job_execution          | 批處理任務處理的相關記錄 |
| batch_job_execution_context  | 任務處理的上下文操作     |
| batch_job_execution_params   | 處理任務相關參數         |
| batch_job_execution_seq      | 任務執行的序號表         |
| batch_job_instance           | 處理任務實例             |
| batch_job_seq                | 任務處理的序號表         |
| batch_step_execution         | 任務步驟處理的相關記錄   |
| batch_step_execution_context | 任務步驟處理的上下文操作 |
| batch_step_execution_seq     | 任務步驟處理的序號表     |

