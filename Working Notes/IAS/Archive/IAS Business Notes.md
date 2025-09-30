# Note

## Guide

1. IAS 提供處理授權 API（JSON 格式），接收並處理授權，更新数据，做出应答（批准或拒絕），將結果返回給 SRS
2. IAS 作爲 Front Office，須依據 IBO 的賬戶結構規則，建立相應的 data structure（Card，Account，Customer 數據），並維護 online 額度信息，負責授權的校驗，應答並更新授權相關數據；之後，將成功處理（核准或拒絕）授權交易以 online 方式發送給 IBO；每日从 IBO 接收最新的“BATCH 處理后的最新額度”，IAS 依據此額度，從 IAS Cutover time 開始，追捕所有交易直至當前時刻，並以更新後的額度，替換目前 online 的授權額度，處理後續授權。
3. [異步訊息(Kafka)](https://bbs.huaweicloud.com/blogs/259907)
4. IAS 與 SRS 之間的授權電文接口，采用一套 JSON 格式（支援 V/M/NCCC/AMEX 等) 。
   IAS 内部，也將采用 Json 電文格式，進行授權處理，並存儲三種類型電文：
    1. Outstanding AUTH Transaction
    2. Received Authorization log
    3. Response Authorization log
5. 未來 IAS 系統
   ![[Pasted image 20230210135125.png]]
    1. 針對信用卡和 Debit Card，目前 BS24 的 PRE-SCREEN 檢核功能，均由 IAS 的授權檢核項替代
        1. IAS 通過授權檢核-外部風險檢核（針對圖中 1)
        2. 針對信用卡，IAS 可進行額度檢核和更新，無需訪問 IBO
        3. 針對 Debit card，IAS 仍需去銀主機，進行額度的檢核和更新
        4. “外部風險檢核”和“去銀主機額度檢核、更新”，都需列入 IAS 關於 DEBIT CARD 的授權檢核項（Debit Card 整理架構，未來還會再討論，是否延續原架構待定）
        5. IAS 需客制與 EFM 的接口及相關電文處理
    2. 接口層進行資料驗證與補齊、名單判斷等處理後，並將完整資料拋送至 ODE 即時評分引擎。
    3. ODE 進行偽冒偵測的即時判斷，再將准駁結果返回至接口層。
    4. 返回 IAS，IAS 需客制與 EFM 的接口及相關電文處理（針對圖中 4）
    5. IAS 通過授權檢核-外部風險更新（針對圖中 5）
    6. 接口層將回覆訊息返回至 BS24 主機。 (非同步方式，直接回覆 0130、0230、0430)
    7. 接口層將准駁結果與交易後補充資料傳送至 ODE，由 ODE 更新最終准駁結果與交易後補充資料至 MEH、SOR、TDR 資料庫。（非同步方式）
        1. IAS 需客制與 EFM 的交互接口（包括檢核和通知兩種功能）
6. 繳款授權處理流程，支援繳款即時恢復額度
   ![[Pasted image 20230210142158.png]]
7. WLPS IAS 在處理授權檢核時，是以授權交易碼為基礎，依據不同的產品碼，可參數定制不同的授權檢核内容以及相應的檢核順序
    1. 交易碼的相關參數
       ![[Pasted image 20230210144208.png]]
    2. 交易檢核項與檢核順序
    3. 交易處理規則
    4. 授權檢核模式
        1. full check
        2. fast check
    5. 授權返回碼
    6. 授權規則設定
8. 發卡授權處理
   ![[Pasted image 20230210144400.png]]
9. 消息類型
    1. Request(100/200) - 准類、拒絕類
    2. Advice(120/220) - 通知類
    3. Void&Reversal(400/200) - 冲正類
10. 卡片分 EMV 和 Mag Stripe Mode
    ![[Pasted image 20230221170155.png|500]]
11. Event![[Pasted image 20230223133710.png|]]
12.

## 現有功能

1. 流量代碼設定
2. 交易碼的設定
3. 基於交易碼的流量
4. 基於規則機制定制相關
5. 授權檢核順序
6. 授權記錄
7. 授權 log 查詢
8. 人工授權、强制授權、人工取消
9. 强制授權檢核配置
10. 佔額天數及比對容差
11. EMV Key 管理
12. EMV 授權檢核流程
13. VISA ARQC 驗證方式
14. Mastercard ARQC 驗證方式
15. 密碼驗證

---

- 更新卡片機制，到期開卡(Base)

---

1. 產品參數中新增`認同代碼`及`Card Type`欄位，用戶可結合此二欄位，為特定卡片產品建立`產品代碼`
2. An outstanding authorization is when a merchant places a hold on a certain amount of funds on your card in order to verify that you have sufficient funds to pay for the transaction.
3. ISO8583，金融交易卡原始電文-交換電文規範, 是一個由國際標準化組織為。EX:F42、F3 (多餘部分填空格)
   ![[Pasted image 20230210161003.png]]

# Modules

| Module Name  | Name         | Description |     |
| ------------ | ------------ | ----------- | --- |
| CAS          |              |             |     |
| CMS          |              |             |     |
| CPS          |              |             |     |
| IAS          |              |             |     |
| SYS          |              |             |     |
| Cardlite-GUI |              |             |     |
| ANI          |              |             |     |
| CAMM         |              |             |     |
| HSM          | 加密機相關   | SDI 模組    |     |
| FDM          | 風險欺詐模塊 | 對應 P4-035 |     |
| MPB          |              |             |     |
| EFM          |              |             |     |
| SVCS         |              |             |     |
| BMS          |              |             |     |
| EFM          |              |             |     |

![[Pasted image 20231114164949.png]]
![[Pasted image 20231114165257.png]]
