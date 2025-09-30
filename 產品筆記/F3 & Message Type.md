| 交易類型              | Message Type (MTI) | Processing Code (Field 3) | 說明                     | 備註          |
| ----------------- | ------------------ | ------------------------- | ---------------------- | ----------- |
| **一般授權**          | 0100               | 000000                    | Purchase/消費授權          | 一般授權        |
|                   | 0100               | 010000                    | Cash Advance/預借現金      | 現金預借授權      |
|                   | 0100               | 200000                    | Refund/退款              | 退款授權        |
|                   | 0100               | 090000                    | Purchase with Cashback | 購買+提現       |
| **VOID**          | 0400               | 000000                    | Void Purchase          | 撤銷購買交易      |
|                   | 0400               | 010000                    | Void Cash Advance      | 撤銷預借現金      |
|                   | 0400               | 200000                    | Void Refund            | 撤銷退款        |
|                   | 0420               | 000000                    | Void Advice            | VOID 通知訊息   |
| **REVERSAL**      | 0400               | 000000                    | Purchase Reversal      | 一般交易沖正      |
|                   | 0400               | 010000                    | Cash Advance Reversal  | 預借現金沖正      |
|                   | 0400               | 200000                    | Refund Reversal        | 退款沖正        |
|                   | 0420               | 000000                    | Reversal Advice        | 沖正通知訊息      |
| **VOID REVERSAL** | 0400               | 000000                    | Void Reversal Purchase | 對 VOID 的再沖正 |
|                   | 0420               | 000000                    | Void Reversal Advice   | VOID 沖正通知   |
|                   |                    | 760000                    | RECURRING              |             |
|                   |                    |                           |                        |             |

|        |               |        |           |                |
| ------ | ------------- | ------ | --------- | -------------- |
| **00** | Purchase      | 000000 | 一般消費  | 最常見交易     |
| **01** | Cash Advance  | 010000 | 現金預借  | ATM 取現等     |
| **02** | Void          | 020000 | 交易撤銷  | 當日撤銷       |
| **03** | Void Reversal | 030000 | VOID 沖正 | 對 VOID 的修正 |
| **09** | Recurring     | 090000 | 定期付款  | 週期性交易     |
| **20** | Refund        | 200000 | 退款      | 商品退貨       |
| **22** | Void Refund   | 220000 | 退款撤銷  | 撤銷退款操作   |
