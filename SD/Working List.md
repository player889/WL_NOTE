- [x] SD P4-007
    - [x] Flow Char
    - [x] API
    - [x] 檔表
    - [x] SQL - DB Schema
    - [x] Class Diagram
    - [ ] check list for CTBC 🆔 zkwaa5


**P4-007 BASE 功能**

---

- [ ] ALL FUNCTION LIST
- [ ] JIRA
- [ ] CTBC 模組
    - [ ] cas-app
    - [ ] ias-app - ready to test
    - [ ] ias-gui
    - [ ] ias-database
    - [ ] IAS-sync-agent

---

DATABASE

- AccountCreditData

--> OutstandingAuthorization

---

- 更改目前的程式碼，COLUMN NAMING
- 變更程式 myBatis-> JPA \*\*
- 將 IAS DB 移到 IBO 內部(Gao Yuan 建議)，可減少同步問題(Kafka)

| 優                  | 缺                   |
| ------------------- | -------------------- |
| 與 OPTIMUS 相同架構 | 需要花時間修改程式碼 |

1. 修改提供給 CTBC 的 TABLE Schema - renaming the column rule as paysuite. (SD)
   ->使用 SQL SCRIPT 修改 TABLE Schmea (naming),後續需要進行程式的變更。

    1. 移除 IAS 不需要的 TABLE
       -> 需要進行測試
    2. 清理 IAS 跟 IBO Sync TABLE 欄位清單
       -> 需要在程式碼內，查看目前有的 SYNC 欄位，並製作成一個檔案，如可能，需要補上 BASE 缺少的 SYNC 資料(EX:APC)。

2. 根據修改 Table Schema，測試 IAS Serivce
   ->需要更改程式碼，以及授權測試
3. 建置給 CTBC 的 IAS 微服務 <-- @@"
4. SD 交付之後(7/1 導讀會)，進行 IAS 開發。 (2-3 月)
    1. BASE IAS 功能尚未提供，開發階段如需要包含 BASE 功能，需要 BASE 提供商業邏輯。
    2. 開發 CTBC 客製化功能


---


Void (取消交易），就只用一個方式 --- 卡號 + F41 TID + F37 RRN
Reversal(沖正) - 用 卡號 + F11  (System Trace No) + F41 (TID)

![[Pasted image 20240607155206.png]]