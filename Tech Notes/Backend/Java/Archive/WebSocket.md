為什麼需要WebSocket?

大家都知道以前客戶端想知道服務端的處理進度，要不停地使用 Ajax 進行輪詢，讓瀏覽器隔個幾秒就向伺服器發一次請求，這對伺服器壓力較大。另外一種輪詢就是採用 long poll 的方式，這就跟打電話差不多，沒收到訊息就一直不掛電話，也就是說，客戶端發起連線後，如果沒訊息，就一直不返回 response 給客戶端，連線階段一直是阻塞的。

而 WebSocket 解決了 HTTP 的這幾個難題。當伺服器完成協議升級後（ HTTP -> WebSocket ），服務端可以主動推送資訊給客戶端，解決了輪詢造成的同步延遲問題。由於 WebSocket 只需要一次 HTTP 握手，服務端就能一直與客戶端保持通訊，直到關閉連線，這樣就解決了伺服器需要反覆解析 HTTP 協議，減少了資源的開銷。

訊息代理（message broker）與客戶端（client）

資訊的傳輸是通過主題（topic）管理的

messagingTemplate.convertAndSendToUser(chat.getTo(), "/queue/chat", chat);
消息的最终发送路径是 "/user/用户名/queue/chat"。


@MessageMapping("/hello")
config.setApplicationDestinationPrefixes("/app");
stompClient.send("/app/hello", {

@Override
public void configureMessageBroker(MessageBrokerRegistry config) {
    config.enableSimpleBroker("/topic", "/user", "/subscribe");
    config.setUserDestinationPrefix("/user");
    config.setApplicationDestinationPrefixes("/app");
}