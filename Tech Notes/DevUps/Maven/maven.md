mvn 命令格式：mvn [options] [<goal(s)>] [<phase(s)>]    其中：-U  和 [options] 和 [goals] 位置沒有關係

mvn 命令參數:

| 參數          | 說明                                                                                                                                |                                                   |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------- |
| mvn ==-B==    | --batch-mode 在非交互（批處理）模式下運行(該模式下,當 Mven 需要輸入時,它不會停下來接受用戶的輸入,而是使用合理的默認值);             | --strict-checksums 如果校驗碼不匹配的話,構建失敗; |
| mvn -c        | --lax-checksums 如果校驗碼不匹配的話,產生告警;                                                                                      |                                                   |
| mvn -cpu      | --check-plugin-updates 對任何相關的註冊插件,強製進行最新檢查(即使項目 POM 裡明確規定了 Maven 插件版本,還是會強製更新);              |                                                   |
| mvn -Dxxx=yyy | 指定 Java 全局屬性;                                                                                                                 |                                                   |
| mvn ==-e==    | --errors 控製 Maven 的日誌級別,產生執行錯誤相關消息;                                                                                |                                                   |
| mvn -emp      | --encrypt-master-password <password> 加密主安全密碼,存儲到 Maven settings 文件裡;                                                   |                                                   |
| mvn -ep       | --encrypt-password <password> 加密伺服器密碼,存儲到 Maven settings 文件裡;                                                          |                                                   |
| mvn -f        | --file <file> 強製使用備用的 POM 文件;                                                                                              |                                                   |
| mvn -fae      | --fail-at-end 僅影響構建結果,允許不受影響的構建繼續;                                                                                |                                                   |
| mvn -ff       | --fail-fast 遇到構建失敗就直接退出;                                                                                                 |                                                   |
| mvn -fn       | --fail-never 無論項目結果如何,構建從不失敗;                                                                                         |                                                   |
| mvn -gs       | --global-settings <file> 全局配置文件的備用路徑;                                                                                    |                                                   |
| mvn -h        | --help 顯示幫助信息;                                                                                                                |                                                   |
| mvn -N        | --non-recursive 僅在當前項目模塊執行命令,不構建子模塊;                                                                              |                                                   |
| mvn -npr      | --no-plugin-registry 對插件版本不使用~/.m2/plugin-registry.xml(插件註冊表)裡的配置;                                                 |                                                   |
| mvn -npu      | --no-plugin-s 對任何相關的註冊插件,不進行最新檢查(使用該選項使 Maven 表現出穩定行為，該穩定行為基於本地倉庫當前可用的所有插件版本); |                                                   |
| mvn -o        | --offline 運行 offline 模式,不聯網更新依賴;                                                                                         |                                                   |
| mvn -pl       | --module_name 在指定模塊上執行命令;                                                                                                 |                                                   |
| mvn -Pxxx     | 激活 id 為 xxx 的 profile (如有多個，用逗號隔開);                                                                                   |                                                   |
| mvn -q        | --quiet 控製 Maven 的日誌級別,僅僅顯示錯誤;                                                                                         |                                                   |
| mvn ==-s==    | --settings <arg> 用戶配置文件的備用路徑;                                                                                            |                                                   |
| mvn ==-U==    | 強製更新 snapshot 類型的插件或依賴庫(否則 maven 一天只會更新一次 snapshot 依賴);                                                    |                                                   |
| mvn -up       | --update-plugins [mvn -cpu]的同義詞;                                                                                                |                                                   |
| mvn -v        | --version  顯示版本信息;                                                                                                            |                                                   |
| mvn -V        | --show-version 顯示版本信息後繼續執行 Maven 其他目標;                                                                               |                                                   |
| mvn -X        | --debug 控製 Maven 的日誌級別,產生執行調試信息;                                                                                     |                                                   |
|               |                                                                                                                                     |                                                   |

```ad-important
title: Malformed \uxxxx encoding while mvn install
collapse: open
deleting "resolver-status.properties" under .m2 folder
```

<span style="color:red;">`fas:ExclamationCircle`</span> .flattened-pom.xml is out of date

> mvn clean

mvn dependency:resolve -U
