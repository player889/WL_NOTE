```json

{
  "@type" : "NormalPayment",
  "id" : "0446fdb1-947d-451c-8872-9f36b720f906",
  "dateTime" : "2021-03-01 16:21:58",
  "retrievalKey" : "HYPERCOM|041100|00000001",
  "interchange" : "acq-kbank-pos",
  "incomingRevRetrievalKey" : "HYPERCOM|041100|00000001",
  "card" : {
    "number" : "4888880000000020",
    "expiryDate" : "2607",
    "scheme" : "VISA",
    "onUsCard" : true
  },
  "account" : {
    "type" : "UNKNOWN"
  },
  "acceptor" : {
    "externalId" : "000000000000001",
    "externalTerminalId" : "00000001",
    "currencyCode": "840"
  },
  "acquirer" : { },
  "forwarder" : { },
  "messageType" : "0200",
  "sourceProtocol" : "KBANK_POS",
  "sourceHeader" : {
    "headerField3" : "1",
    "headerField4" : "11",
    "headerField5" : "11"
  },
  "processingCode" : "00000000",
  "traceNumber" : "041100",
  "reversible" : true,
  "hostSpecificData" : { },
  "cardholderTransactionChecks" : { },
  "completionRule" : "UNKNOWN",
  "authorisation" : true,
  "circumstance" : {
    "_track2" : "4023657095596120=2404",
    "_decomposedIsoTrack" : {
      "cardNumber" : "4023657095596120",
      "expiryDate" : "2404",
      "errors" : [ ]
    },
    "posConditionCode" : "00",
    "localDate": "2022-05-17",
    "localTime": "12:20:00",
    "operationTime": "2022-05-17 12:20:00"
  },
  "exchangeRate" : { },
  "cardholder" : { },
  "environment" : { },
  "dcc" : {
    "enable" : false
  },
  "externalSetId" : "KBANK_POS|||00000001",
  "authorisationStep" : "INITIAL",
  "amount" : {
    "acceptorView" : 1100
  },
  "type" : "PAYMENT",
  "tipAmount" : { },
  "step" : "PAYMENT",
  "qrpay" : false,
  "internalTransactionType" : "PURCHASE"
}

```
