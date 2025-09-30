Cardlite GUI
4988000000000065
4966000000000160
4966000000000343
4966000000000210

SIT card number
4966000000000160

UAT Card number
5100123400000012 -- CTBC_TWD
5100123400000020 -- DD

IAS 修改授權查詢 GUI，顯示 CTBC 客制相關 field
IAS 在查詢授權 log 時，增加輸入 mid 查询条件，可以依據卡號、MID、授權日期(range)，進行授權 log 查詢

crdAcrTmlTxt: String # 终端号
crdAcrIdnTxt: String # 商户号

终端号 "crdAcrTmlTxt": "TERMID01", Card Acceptor Terminal abo => Acceptance Device
商户号 "crdAcrIdnTxt": "CARD ACCEPTOR" --> (ABO) merchant settlment account - External ID [009900000291672]
POS 数据代码 "psvDtaCdeTxt"

收单机构识别号 map to column AQR_ITN_IDN_NBR, int
