1. VISA、MASTER CARD、NCCC

ng build @custom/ias --prod

NCCC

filter 有沒有缺別

4 - base
5 - customized

---

1. 1:預借現金 - processing code = 20
2. 2:彈性額度　- ??
3. 3:網路交易　－　 transaction object
4. 4:交易必須簽名 - transaction object

---

1. 新舊卡同卡號授權時，新卡開卡後，舊卡不可進行授權
   CARD 裡面的 NEW_CARD_ID 與 OLD_CARD_ID，判斷 OLD_CARD_ID 是否能夠使用，因 NEW_CARD_ID 開卡的時候，舊卡就不能使用。
2. 紅框 - decline reason / Ext. Response Code (return to SRS, and SRS using mapping table return to outer service)
   ![[Pasted image 20240718143743.png]]
3. RRN F37