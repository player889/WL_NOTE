![[Pasted image 20250312135817.png]]
```cURL
curl --location 'http://127.0.0.1:8058/graphql?lang=en' \
--header 'Accept: application/json, text/plain, */*' \
--header 'Accept-Language: en-US,en;q=0.9,zh-TW;q=0.8,zh;q=0.7' \
--header 'Cache-Control: no-cache' \
--header 'Connection: keep-alive' \
--header 'Content-Type: application/json' \
--header 'Origin: http://localhost:4200' \
--header 'Pragma: no-cache' \
--header 'Referer: http://localhost:4200/' \
--header 'Sec-Fetch-Dest: empty' \
--header 'Sec-Fetch-Mode: cors' \
--header 'Sec-Fetch-Site: cross-site' \
--header 'User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/123.0.0.0 Safari/537.36' \
--header 'X-Token: 123456' \
--header 'sec-ch-ua: "Google Chrome";v="123", "Not:A-Brand";v="8", "Chromium";v="123"' \
--header 'sec-ch-ua-mobile: ?0' \
--header 'sec-ch-ua-platform: "Windows"' \
--data '{"query":"query AuthResponseCode_selectPage($query: String!) {\n  AuthResponseCode_selectPage(query: $query) {\n    totalCount\n    pageSize\n    currentPage\n    data {\n      id\n      createTime\n      atnCdeTxt\n      __typename\n    }\n    __typename\n  }\n}\n","variables":{"query":"{\"where\":[],\"page\":1,\"pageSize\":10,\"orderBy\":[]}"}}'
```